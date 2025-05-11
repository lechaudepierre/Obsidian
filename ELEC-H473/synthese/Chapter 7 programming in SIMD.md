
# Summary: ELEC-H-473 Th07 - Programming with SIMD

This document summarizes the key concepts related to programming using SIMD (Single Instruction, Multiple Data) instructions, primarily using digital image processing examples, as presented in the lecture slides.

## 1. Digital Image Processing Basics & C (Slides 2-9)

* **Digital Image Representation:**
    * An image is a grid of **pixels** (picture elements).
    * Pixel color is often represented by **RGB** (Red, Green, Blue) values, or grayscale.
    * **Sampling:** Converting a continuous scene into a discrete pixel grid.
    * **Quantification:** Assigning a digital integer value to each pixel's color component (e.g., 8-bit depth gives 256 levels per component).
    * **Grayscale:** Often 8-bit (0=black, 255=white). Can be represented in color as R=G=B.
* **Image Sensors (APS):** CMOS Active Pixel Sensors perform Analog-to-Digital conversion (Slide 4, 6).
* **Image Representation in C:**
    * Typically stored as 1D arrays in memory (`unsigned char *`).
    * Requires knowing image Width (W) and Height (H) to interpret the 1D array as a 2D image (e.g., pixel at `(x, y)` is at index `y * W + x`).
    * **RAW file format:** Contains only pixel data, no header. Requires W & H to be known separately.
* **C Code Example: Reading/Writing RAW Image (Slides 8, 9):**
    ```c
    #include <stdio.h>
    #include <stdlib.h>

    int main() {
        int W = 1024, H = 1024; // Example dimensions
        unsigned char *src = NULL;
        unsigned char *dst = NULL;
        FILE *fp1 = NULL, *fp2 = NULL;
        size_t image_size = (size_t)W * H * sizeof(unsigned char);

        // 1. Allocate memory
        src = (unsigned char *) malloc(image_size);
        dst = (unsigned char *) malloc(image_size);
        if (src == NULL || dst == NULL) {
            printf("Out of memory!\n");
            exit(1);
        }

        // 2. Read source image
        fp1 = fopen("image_src.raw", "rb"); // Use "rb" for binary read
        if (fp1 != NULL) {
            fread(src, sizeof(unsigned char), W * H, fp1);
            fclose(fp1);
        } else {
            printf("Can't open source file!\n");
            // Initialize src with some pattern if file not found for testing
            for(size_t i=0; i<image_size; ++i) src[i] = i % 256;
            // exit(1); // Or exit if file is mandatory
        }

        // 3. --- IMAGE PROCESSING HAPPENS HERE ---
        // Example: Copy src to dst (replace with actual processing)
        for(size_t i=0; i<image_size; ++i) dst[i] = src[i];
        // --- END OF PROCESSING ---

        // 4. Write destination image
        fp2 = fopen("image_dst.raw", "wb"); // Use "wb" for binary write
        if (fp2 != NULL) {
            fwrite(dst, sizeof(unsigned char), W * H, fp2);
            fclose(fp2);
        } else {
            printf("Can't open destination file!\n");
            // exit(1); // Or exit
        }

        // 5. Free memory
        free(src);
        free(dst);
        printf("Processing complete.\n");
        return 0;
    }
    ```

## 2. Basic Image Arithmetic (Slides 10-18)

Applying simple arithmetic operations pixel-wise.

* **Adding an Offset (Brightness Increase):**
    * Increases pixel values, making the image brighter.
    * **Saturation:** Pixel values are typically `unsigned char` (0-255). Adding an offset might exceed 255. The result must be clamped (saturated) to 255.
    * *C Implementation (Slide 13):*
        ```c
        // Inside a loop iterating through all pixels (index i)
        int tmp;
        unsigned char offset = 50;
        tmp = (int)(src[i]) + (int)(offset); // Use int for intermediate calc
        if (tmp > 255) {
            dst[i] = 255; // Saturate to max
        } else {
            dst[i] = (unsigned char)tmp;
        }
        // Note: Can also saturate to 0 if subtracting offset results in < 0
        ```
    * *SIMD Implementation (Adding Offset) (Slide 15):*
        * Load a vector (e.g., 16 bytes using `movdqu`) from the source image (`xmm0`).
        * Load a vector containing the offset value replicated 16 times (`xmm1`).
        * Use a packed byte addition instruction. **Crucially, use the saturating version** (`paddusb` - Packed Add Unsigned Bytes with Saturation) to handle overflow automatically (Slide 16).
        * Store the resulting vector (`movdqu`).
        * Loop over the image in 16-byte chunks.
        ```assembly
        // Simplified conceptual loop structure (registers esi=src, edi=dst)
        mov esi, srcA        ; Source image pointer
        mov edi, dst         ; Destination image pointer
        mov ecx, loop_count  ; Number of 16-byte chunks
        movdqu xmm1, [offset_vector] ; Load vector of {50, 50,...}

        loop11:
          movdqu xmm0, [esi]     ; Load 16 bytes from src image
          paddusb xmm0, xmm1     ; Add 16 bytes with unsigned saturation
          movdqu [edi], xmm0     ; Store 16 bytes to dst image
          add esi, 16            ; Advance src pointer
          add edi, 16            ; Advance dst pointer
          sub ecx, 1             ; Decrement loop counter
          jnz loop11             ; Jump if not zero
        ```
* **Adding Two Images:**
    * Combines two images pixel by pixel. Useful for overlays.
    * Requires saturation, similar to adding an offset.
    * *C Implementation (Slide 17):* Similar loop, `tmp = (int)src1[i] + (int)src2[i];`, check saturation, store in `dst[i]`.
    * *SIMD Implementation (Slide 18):* Load vectors from `src1` (`xmm0`) and `src2` (`xmm1`), use `paddusb`, store result.

## 3. Computing Threshold Image (Slides 19-24)

Creating a binary (black and white) image based on a threshold value.

* **Concept (Slide 20):** If a pixel value is below the threshold, set it to 0 (black); otherwise, set it to 255 (white).
* *C Implementation (Slide 22):*
    ```c
    // Inside a loop iterating through all pixels (index i)
    unsigned char threshold = 128; // Example threshold
    if (src[i] < threshold) {
        dst[i] = 0;
    } else {
        dst[i] = 255;
    }
    ```
* *SIMD Implementation (Slide 24):*
    * Requires a comparison instruction. `pcmpeqb xmm0, xmm7` compares packed bytes in `xmm0` and `xmm7` for equality. It sets the corresponding byte in the destination (`xmm0`) to all 1s (`0xFF`) if equal, and all 0s (`0x00`) if not equal.
    * To implement thresholding (`src[i] < threshold`), you often compare against a threshold vector and use the resulting mask (`0xFF` for true, `0x00` for false) with bitwise operations (like AND, AND NOT, OR) to select between 0 and 255.
    * The example on Slide 24 seems simplified. A more complete approach might involve:
        1.  Load source data (`xmm0`).
        2.  Load threshold vector (`xmm7`).
        3.  Use `pmaxub` (or `pminub`) to effectively clamp values relative to the threshold, or `pcmpgtb` (compare greater than) if available.
        4.  Alternatively, use `pcmpgtb` to create a mask (bytes are `0xFF` if `src > threshold`, `0x00` otherwise).
        5.  Store the resulting mask (which directly represents the 0/255 thresholded image).
    * Note the use of aligned moves (`movapd`) in the slide example, implying data must be aligned.

## 4. Non-linear Image Filters (Max/Min) (Slides 25-36)

Spatial filters where the output pixel depends on a neighborhood of input pixels.

* **Concept (Slide 26):** Calculate the output pixel based on the values in a window (e.g., 3x3) around the input pixel.
    * **Max Filter:** Output pixel = maximum value in the neighborhood. Used for dilation.
    * **Min Filter:** Output pixel = minimum value in the neighborhood. Used for erosion.
    * **Median Filter:** Output pixel = median value in the neighborhood. Good for noise reduction (salt & pepper noise).
* **SIMD Algorithm for Max/Min (3x3 example) (Slides 29-31):**
    1.  Load 3 consecutive rows (L1, L2, L3) into SIMD registers.
    2.  Compute column-wise max/min: `R1 = Max(L1, L2, L3)` using packed max/min instructions (e.g., `pmaxub`). `R1` now holds the maximum value for each column across the 3 rows.
    3.  Duplicate `R1` into `R2` and `R3`.
    4.  Shift `R2` right by 1 byte (`psrldq R2, 1`).
    5.  Shift `R3` right by 2 bytes (`psrldq R3, 2`).
    6.  Compute the final maximum: `Result = Max(R1, R2, R3)` using `pmaxub`. The result vector now contains the 3x3 maximum for each possible center pixel position within the initial vector load.
* **Border Issues (Slides 32, 33):**
    * **Window Border:** The shifting process means the first `n-1` elements of the result vector (where `n` is the filter width, e.g., 3) are invalid as they didn't have a full neighborhood. Only `VectorSize - (n-1)` results are valid per vector operation.
    * **Image Border:** Pixels near the image edge don't have a full neighborhood. These are typically ignored, resulting in a slightly smaller output image or filled with a constant color/replication.
* **Relevant SIMD Instructions (Slide 34):**
    * `pmaxub` / `pminub`: Packed Maximum/Minimum Unsigned Bytes.
    * `psrldq`: Packed Shift Right Logical Double Quadword (shifts by whole bytes).
* **SIMD Implementation (Max Filter) (Slide 35):** Shows the assembly code implementing the described algorithm, loading 3 rows (using offsets like `[esi+1024]`, `[esi+2048]` assuming image width 1024), performing column max, duplicating, shifting, performing row max, and storing the result. Note the pointer increments reflect the valid output size (14 bytes for a 16-byte vector and 3x3 filter).

## 5. Image Morphing (Slides 37-42)

Blending between two images A and B using a factor alpha (`α`).

* **Formula:** `Z = A * α + B * (1 - α)` or rearranged as `Z = B + α * (A - B)` (Slide 38).
* **Challenge:** Intermediate calculations (`A - B`) might be negative or exceed the range of `unsigned char`. Calculations need to be done using a wider type (e.g., 16-bit words).
* **SIMD Algorithm Flow (Slide 39):**
    1.  Load bytes from images A and B.
    2.  **Unpack** bytes into words (e.g., 8 bytes -> 8 words) using `punpcklbw` (Unpack Low Bytes to Words). This interleaves the source bytes with zeros (if the second operand is zeroed) or another register's bytes.
    3.  Perform subtraction `(A - B)` using packed word subtraction (`psubw`).
    4.  Multiply `(A - B)` by alpha (`α`) using packed word multiplication (`pmulhw` - Packed Multiply High Word, keeps the high 16 bits of the 32-bit result, effectively scaling by alpha if alpha is pre-scaled).
    5.  Add `B` (unpacked to words) using packed word addition (`paddw`).
    6.  **Pack** the resulting words back into bytes with saturation (`packuswb` - Pack Words to Bytes with Unsigned Saturation).
    7.  Store the resulting byte vector.
* **Relevant SIMD Instructions (Slides 40, 41):** `punpcklbw`, `pmulhw`, `packuswb`.
* **SIMD Implementation Snippet (Slide 42):** Shows the core steps using intrinsics-like names for clarity.

## 6. Vectorization Counter Example: Histogram (Slides 43-47)

An example where SIMD is difficult to apply effectively.

* **Histogram Concept (Slide 44):** Count the frequency of each pixel intensity value (0-255) in an image. Result is an array (histogram bins) where `histo[i]` stores the count of pixels with value `i`.
* *C Implementation (Slide 46):* Simple loop iterates through image pixels. For each pixel `src[i]`, it increments the corresponding bin: `histo[src[i]]++;`.
* **SIMD Challenge (Slide 47):**
    * The core operation `histo[src[i]]++` involves **indirect memory access** based on the *data* (`src[i]`).
    * Multiple pixels within a single SIMD vector might have the *same* intensity value. If processed in parallel, they would all try to increment the *same* histogram bin simultaneously, leading to a **race condition** and incorrect counts.
    * While complex "gather" (for reading `src[i]`) and "scatter" (for writing `histo[...]`) instructions exist in later SIMD versions (like AVX2/AVX-512), and atomic operations could be used, implementing histograms efficiently in SIMD is non-trivial and often requires different algorithms (e.g., computing partial histograms per core/thread and merging later) rather than direct vectorization of the simple C code.

## 7. Complex Number Multiplication (Slides 48-54)

An example showing how specialized SIMD instructions simplify certain tasks.

* **Formula:** `(a + bi) * (c + di) = (ac - bd) + i * (ad + bc)`
* **SIMD Algorithm Idea (Slide 49):** Load `a, b` and `c, d`. Requires shuffling/duplicating elements to perform the necessary multiplications (`ac`, `bd`, `ad`, `bc`) and then combine them with additions/subtractions.
* **Specialized Instructions (SSE3+) (Slides 50-52):**
    * `movddup`: Loads a 64-bit double and duplicates it into both halves of a 128-bit XMM register (useful for getting `c, c` and `d, d`).
    * `shufpd`: Shuffles packed double-precision floats between two registers based on an immediate mask (useful for getting `b, a` from `a, b`).
    * `addsubpd`: Performs subtraction on the low half and addition on the high half of packed doubles (perfect for calculating `ac-bd` and `ad+bc` simultaneously).
* **Comparison (Slide 54):** Shows how SSE3 code using these specialized instructions is much shorter and more efficient than implementing the same logic using only basic SSE2 instructions.

## 8. Move Instructions in SIMD (Slides 55-62)

Choosing the right instruction to move data to/from SIMD registers is important for performance.

* **Aligned vs. Unaligned (Slide 57):**
    * Aligned moves (`movaps`, `movapd`, `movdqa`) require memory addresses to be on specific boundaries (e.g., 16-byte for XMM). Faster, but cause exceptions if address is misaligned.
    * Unaligned moves (`movups`, `movupd`, `movdqu`) work with any address but are generally slower due to extra hardware work.
* **Data Type Hints (Slide 58):** Using the correct move instruction for the data type (e.g., `movaps` for single-precision float, `movapd` for double, `movdqa`/`movdqu` for integer) can help the CPU's internal scheduling by hinting how the data will be used, potentially avoiding latency. Using the "wrong" type usually works but might incur a small penalty.
* **Cache-Split Avoidance (Slide 59):** `lddqu` (Load Double Quadword Unaligned) is specifically designed to minimize the penalty of unaligned loads that cross cache line boundaries, potentially loading a larger block internally. May make subsequent stores slower.
* **Non-Temporal Moves (Streaming Stores) (Slide 60):**
    * Instructions (`movnt...` like `movntdq`, `movntps`) hint to the processor that the data being loaded/stored will likely *not* be reused soon.
    * These operations often **bypass the cache hierarchy**, writing directly to/reading directly from memory (using special internal buffers).
    * Avoids polluting the cache with data that won't be needed again.
    * Requires aligned addresses.
* **Smaller Moves (Slide 61):** Instructions to move 32-bit (`movd`) or 64-bit (`movq`, `movsd`, `movss`) values into/out of larger SIMD registers. Pay attention to whether they zero-extend the remaining bits (`movd`, `movq`) or leave them untouched (`movss`, `movsd` for reg-reg moves). `movlps`/`movhps` target specific halves.

## 9. Measuring Program Performance (Slides 63-69)

How to time code execution accurately.

* **Standard C `time()` (Slide 64):**
    * `#include <time.h>`
    * `time_t time(time_t *tloc);`
    * Returns seconds since the epoch (Jan 1, 1970).
    * **Resolution:** Only 1 second. Too coarse for performance measurement.
* **Standard C `clock()` (Slide 65, 66):**
    * `#include <time.h>`
    * `clock_t clock(void);`
    * Returns processor time consumed by the program in "clock ticks" since program start.
    * Divide by `CLOCKS_PER_SEC` (often 1,000,000) to get seconds.
    * **Resolution:** Typically milliseconds (e.g., 1ms). Still often too coarse. Actual resolution depends on OS timer interrupt frequency (Slide 67).
* **Windows High-Resolution Timer (QPC) (Slide 68, 69):**
    * `#include <windows.h>`
    * `QueryPerformanceFrequency(LARGE_INTEGER *lpFrequency);` Gets the high-resolution counter frequency (ticks per second, fixed at boot). Call once.
    * `QueryPerformanceCounter(LARGE_INTEGER *lpPerformanceCount);` Gets the current high-resolution counter value (ticks).
    * **Usage:** Call `QueryPerformanceCounter` before and after the code section. Subtract values to get elapsed ticks. Convert to microseconds: `ElapsedMicroseconds = (ElapsedTicks * 1000000) / Frequency;`
    * **Resolution:** Typically sub-microsecond (< 1µs). Suitable for timing small code sections.
