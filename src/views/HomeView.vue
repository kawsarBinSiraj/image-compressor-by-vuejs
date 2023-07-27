<script setup>
import { ref, onUpdated } from "vue";
import Compressor from "compressorjs";
import { useDropzone } from "vue3-dropzone";

/**
 * @desc :- satates
 * created_by  :- Kawsar Bin Siraj
 * created_at  :- 24/07/2023 17:45:10
 */
const imgQuality = ref("2");
const originalImgInfo = ref(null);
const compressedImgInfo = ref(null);
const originalImg = ref("");
const compressedImg = ref("");
const { getRootProps, getInputProps, ...rest } = useDropzone({ onDrop, accept: "image/*" });

/**
 * @desc :- onImgUpload func
 * created_by  :- Kawsar Bin Siraj
 * created_at  :- 24/07/2023 17:45:20
 */
function onDrop(files, rejectReasons) {
    let selectedFile = files[0];
    if (!selectedFile) return;

    new Compressor(selectedFile, {
        quality: imgQuality.value / 10,
        convertTypes: ["image/jpg", "image/png", "image/jpeg"],
        convertSize: Infinity,
        // The compression process is asynchronous,
        // which means you have to access the `result` in the `success` hook function.
        success(result) {
            originalImgInfo.value = selectedFile;
            compressedImgInfo.value = result;
            originalImg.value = URL.createObjectURL(selectedFile);
            compressedImg.value = URL.createObjectURL(result);
        },
        error(err) {
            console.log(err.message);
        },
    });
}

/**
 * @desc :- onQualityChange
 * created_by  :- Kawsar Bin Siraj
 * created_at  :- 25/07/2023 14:39:35
 */
const onQualityChange = (e) => {
    imgQuality.value = e.target.value;
    onDrop([originalImgInfo.value], null);
};

/**
 * @desc :- bytesTo
 * created_by  :- Kawsar Bin Siraj
 * created_at  :- 25/07/2023 11:22:58
 */
const bytesTo = (data, to) => {
    const const_term = 1024;
    // Convert the values and concatenate
    // the appropriate unit
    if (to === "KB") {
        return (data / const_term).toFixed(3) + " KB";
    } else if (to === "MB") {
        return (data / const_term ** 2).toFixed(3) + " MB";
    } else if (to === "GB") {
        return (data / const_term ** 3).toFixed(3) + " GB";
    } else if (to === "TB") {
        return (data / const_term ** 4).toFixed(3) + " TB";
    } else {
        return "Please pass valid option";
    }
};
</script>

<template>
    <main>
        <div class="container p-5">
            <div class="row">
                <div class="col-lg-10 m-auto">
                    <img alt="Vue logo" class="logo" src="@/assets/logo.svg" height="80" />
                    <div class="title mb-5">
                        <h3 class="fs-1 fw-normal">Image compressor by <span class="text-success">VUE</span> js</h3>
                        <var
                            >© Designed & Developed by
                            <a href="https://kawsarbinsiraj.github.io/" target="_blank" class="text-primary">Kawsar Bin Siraj</a>
                        </var>
                    </div>
                    <form action="#" method="post">
                        <div class="row">
                            <div class="col-7">
                                <div class="form-group">
                                    <div
                                        v-bind="getRootProps()"
                                        class="p-2 py-3 text-center rounded d-flex gap-3 justify-content-center"
                                        style="border: 2px dashed #333; cursor: pointer"
                                    >
                                        <input class="d-none" v-bind="getInputProps()" />
                                        <svg
                                            xmlns="http://www.w3.org/2000/svg"
                                            width="22"
                                            height="22"
                                            fill="currentColor"
                                            class="bi bi-upload mb-2"
                                            viewBox="0 0 16 16"
                                        >
                                            <path
                                                d="M.5 9.9a.5.5 0 0 1 .5.5v2.5a1 1 0 0 0 1 1h12a1 1 0 0 0 1-1v-2.5a.5.5 0 0 1 1 0v2.5a2 2 0 0 1-2 2H2a2 2 0 0 1-2-2v-2.5a.5.5 0 0 1 .5-.5z"
                                            />
                                            <path
                                                d="M7.646 1.146a.5.5 0 0 1 .708 0l3 3a.5.5 0 0 1-.708.708L8.5 2.707V11.5a.5.5 0 0 1-1 0V2.707L5.354 4.854a.5.5 0 1 1-.708-.708l3-3z"
                                            />
                                        </svg>
                                        <template v-if="originalImgInfo">
                                            <p class="mb-0 text-success">{{ originalImgInfo.name }}</p>
                                        </template>
                                        <template v-else>
                                            <p class="mb-0" v-if="isDragActive">Drop the image here ...</p>
                                            <p class="mb-0" v-else>Drag 'n' drop image here, or click to select</p>
                                        </template>
                                    </div>
                                </div>
                            </div>
                            <div class="col-5">
                                <div class="form-group">
                                    <label for="#" class="form-label d-block mb-1">Quality Density : {{ imgQuality }}</label>
                                    <input type="range" min="0" max="10" class="w-100" :value="imgQuality" @input="onQualityChange" />
                                </div>
                            </div>
                        </div>
                        <div class="row mt-5" v-if="originalImg">
                            <div class="col-lg-6">
                                <h3 class="mb-3 fs-4 fw-normal">
                                    Original Image <var class="text-success">- {{ bytesTo(originalImgInfo.size, "KB") }}</var>
                                </h3>
                                <img :src="originalImg" alt="Original Image" class="img-fluid" />
                            </div>
                            <div class="col-lg-6">
                                <h3 class="mb-3 fs-4 fw-normal d-flex align-items-center">
                                    Compressed Image <var class="text-primary">- {{ bytesTo(compressedImgInfo.size, "KB") }}</var>
                                    <a download :href="compressedImg" class="btn btn-sm btn-primary rounded ms-3 text-light">Download</a>
                                </h3>
                                <img :src="compressedImg" alt="Compressed Image" class="img-fluid" />
                            </div>
                        </div>
                    </form>
                </div>
            </div>
        </div>
    </main>
</template>
