<template>
    <transition name="fade">
        <div class="modal-wrapper" v-show="show">
            <div class="inside-wrapper inside-background">

                <!-- Modal Title 부분 -->
                <div class="modal-title-wrapper modal-title-text" v-if="title !== null">
                    {{ title }}
                    <button class="modal-title-close-button-wrapper" @click="OnClickClose" v-if="hasUpperCloseButton">
                        <img src="/static/images/modal/icon-close-modal.png"/>
                    </button>
                </div>

                <!-- Modal Content 부분 -->
                <div style="padding-top: 60px;">
                    <slot name="modalProps"/>
                </div>

                <!-- Modal Footer 부분 -->
                <div class="modal-footer-wrapper">
                    <button class="modal-footer-cancel-button modal-footer-button" @click="OnClickClose" v-if="hasLowerCloseButton">
                        취소
                    </button>
                    <button class="modal-footer-confirm-button modal-footer-button" @click="OnClickSubmit">
                        확인
                    </button>
                </div>

            </div>
            <div class="outside-wrapper" @click="OnClickClose"></div>
        </div>
    </transition>
</template>

<script>
    export default {
        name: 'BaseModal',
        props: {
            title: {
                type: String,
                default: null
            },
            closeCallback: {
                type: Function,
                default: null
            },
            submitCallback: {
                type: Function,
                default: null
            },
            hasUpperCloseButton: {
                type: Boolean,
                default: true
            },
            hasLowerCloseButton: {
                type: Boolean,
                default: false
            }
        },
        computed: {
            show () {
                return this.$attrs.value
            }
        },
        methods: {
            async OnClickClose () {
                if (this.closeCallback !== null) {
                    await this.closeCallback()
                }
                this.$emit('input', false)
            },
            async OnClickSubmit () {
                if (this.submitCallback !== null) {
                    await this.submitCallback()
                }
                this.$emit('input', false)
            }
        }
    }
</script>

<style scoped>

    .inside-wrapper > hr {
        margin: 0px;
        padding: 0px;
        color: #dcdcdc;
    }

    .outside-wrapper {
        position: absolute;
        height:100vh;
        width:100vw;
        z-index: -1;
    }

    .modal-wrapper {
        position: fixed;
        z-index: 1500;
        left: 0;
        top: 0;
        width: 100%;
        height: 100%;
        overflow: auto;
        background-color: rgb(0, 0, 0);
        background-color: rgba(0, 0, 0, 0.3);
        vertical-align: middle;
    }

    .inside-wrapper {
        position: absolute;
        width: 960px;
        margin-left: calc(50vw - 480px);
        border-radius: 18px;
        background-color: white;
        top: 50%;
        transform: translate(0, -50%);
    }

    .inside-background {
        background-color: white;
    }

    .modal-title-wrapper {
        padding: 60px 60px 0px 60px;
    }

    .modal-title-text {
        text-align: center;
        font-size: 54px;
        font-family: 'NotoSansCJKkr-Medium';
        line-height: 1.04;
        font-weight: 500;
    }

    .modal-title-close-button-wrapper {
        float: right;
        vertical-align: middle;
        line-height: 40px;
        width: 64px;
        height: 64px;
    }

    .modal-footer-wrapper {
        width: 100%;
        display: flex;
        align-items: center;
        justify-content: center;
        padding-bottom: 40px;
    }

    .modal-footer-button {
        flex:1 1 auto;
        margin: 10px;
        color: white;
        height: 140px;
        max-width: 410px;
        border-radius: 18px;
    }

    .modal-footer-cancel-button {
        background-color: #ff5f5f;
    }

    .modal-footer-confirm-button {
        background-color: #188cff;
    }

    .fade-enter-active, .fade-leave-active {
        transition: opacity .2s;
    }
    .fade-enter, .fade-leave-to /* .fade-leave-active below version 2.1.8 */ {
        opacity: 0;
    }
</style>
