
<template>
    <div class="upload" @click="upload">
        <slot></slot>
    </div>
</template>

<script type="text/javascript">
let file
export default {
    name: 'upload',
    // 组件属性、变量、用于接收父组件的传值
    // 变量
    data () {
        return {
            forbidden: false
        }
    },
    props: ['on-success', 'list-type', 'limit', 'before-upload', 'disabled'],
    computed: {},
    // 使用其它组件
    components: {},
    // 方法
    watch: {},
    // 生命周期函数
    created () {
    },
    methods: {
        createUploadIframe (id) {
            let frameId = 'jUploadFrame'
            let io = document.createElement('iframe')
            io.id = frameId
            io.name = frameId
            io.style.position = 'absolute'
            io.style.top = '-1000px'
            io.style.left = '-1000px'
            document.body.appendChild(io)
            return io
        },
        createUploadForm (id) {
            let that = this
            let formId = 'jUploadForm'
            let form = document.createElement('form')
            form.action = window.uploadURL + 'attachment/file/upload'
            form.method = 'post'
            form.name = formId
            form.id = formId
            form.enctype = 'multipart/form-data'
            form.style.position = 'absolute'
            form.style.top = '-1000px'
            form.style.left = '-1000px'
            form.target = 'jUploadFrame'
            if (form.encoding) {
                form.encoding = 'multipart/form-data'
            } else {
                form.enctype = 'multipart/form-data'
            }
            let ipt = document.createElement('input')
            ipt.type = 'file'
            ipt.id = 'ipt'
            ipt.onchange = function (e) {
                file = e.target.files ? e.target.files[0] : ''
                if (that.beforeUpload && file) {
                    if (that.beforeUpload(file)) {
                        document.getElementById('jUploadForm').submit()
                    }
                } else {
                    document.getElementById('jUploadForm').submit()
                }
            }
            ipt.name = 'file'
            form.appendChild(ipt)
            document.body.appendChild(form)
            return form
        },
        ajaxFileUpload () {},
        upload () {
            if (this.disabled === true) {
                return
            }
            if (!document.getElementById('jUploadFrame')) {
                this.createUploadIframe()
            } else {
                document.body.removeChild(document.getElementById('jUploadFrame'))
                this.createUploadIframe()
            }
            if (!document.getElementById('jUploadForm')) {
                this.createUploadForm()
            } else {
                document.body.removeChild(document.getElementById('jUploadForm'))
                this.createUploadForm()
            }
            if (window.attachEvent) {
                document
                    .getElementById('jUploadFrame')
                    .attachEvent('onload', this.uploadCallback)
            } else {
                document
                    .getElementById('jUploadFrame')
                    .addEventListener('load', this.uploadCallback, false)
            }
            document.getElementById('ipt').click()
        },
        uploadCallback () {
            let res = window.frames['jUploadFrame'].document.body.innerHTML
            if (!res) return
            this.onSuccess(JSON.parse(res.replace(/(.+)({.+})(.+)/, '$2')), file)
            setTimeout(() => {
                document.body.removeChild(document.getElementById('jUploadForm'))
                document.body.removeChild(document.getElementById('jUploadFrame'))
            }, 1100)
        }
    }
}
</script>

<style lang="less" scoped>
.upload {
    cursor: pointer;
}
</style>
