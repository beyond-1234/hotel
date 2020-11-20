<template>
    <div>
        <a-upload
            name="avatar"
            list-type="picture-card"
            class="avatar-uploader"
            :show-upload-list="false"
            :before-upload="beforeUpload"
            @change="handleChange"
        >
            <img
                v-if="imageUrl"
                :src="imageUrl"
                alt="avatar"
                style="width: 100%"
            />
            <div v-else>
                <a-icon :type="loading ? 'loading' : 'plus'" />
                <div class="ant-upload-text">Upload</div>
            </div>
        </a-upload>
    </div>
</template>
<script>
// import client from '@/utils/oss'
import moment from "moment";
import { mapGetters, mapActions } from "vuex";
function getBase64(img, callback) {
    var reader = new FileReader();
    reader.addEventListener("load", () => callback(reader.result));
    reader.readAsDataURL(img);
}

export default {
    data() {
        return {
            loading: false,
            imageUrl: "",
        };
    },
    computed: {
        ...mapGetters(["avatar"]),
    },
    mounted() {
        this.imageUrl = this.avatar;
    },
    methods: {
        ...mapActions(["updateAvatar"]),
        uploadPath(path, file) {
            // 上传文件的路径，使用日期命名文件目录
            return `${moment().format("YYYYMMDD")}/${file.name.split(".")[0]}-${
                file.uid
            }.${file.type.split("/")[1]}`;
        },
        UploadImage2(self, file) {
            getBase64(file, (imageUrl) => {
                this.imageUrl = imageUrl;
                // 这里不用处理，到父组件的modal中一起处理
                this.$emit("picture", this.imageUrl);
                console.log(this.imageUrl);
                this.loading = false;
            });
        },
        handleChange(info) {},
        beforeUpload(file) {
            const isJpgOrPng =
                file.type === "image/jpeg" || file.type === "image/png";
            if (!isJpgOrPng) {
                this.$message.error("You can only upload JPG file!");
            }
            const isLt2M = file.size / 1024 / 1024 < 2;
            if (!isLt2M) {
                this.$message.error("Image must smaller than 2MB!");
            }
            this.UploadImage2(this, file);
            //   return isJpgOrPng&&isLt2M; // 不使用默认上传方式
            return false; // 不使用默认上传方式
        },
    },
};
</script>
<style scoped>
.avatar-uploader > .ant-upload {
    width: 128px;
    height: 128px;
}
.ant-upload-select-picture-card i {
    font-size: 32px;
    color: #999;
}

.ant-upload-select-picture-card .ant-upload-text {
    margin-top: 8px;
    color: #666;
}
</style>