<template>
    <transition name="slide">
        <div
            class="base-snackbar-wrapper base-snackbar-text"
            v-if="show"
            :style="{ backgroundColor: targetBackgroundColor }"
        >
            <div class="base-snackbar-contents-wrapper">
                <div class="base-snackbar-contents-left-wrapper" :style="{ color: targetTextColor }">
                    <slot></slot>
                </div>
            </div>
            <div class="base-snackbar-close-wrapper">
                <button @click="closeSnackbar" :style="{ color: targetTextColor }">CLOSE</button>
            </div>
        </div>
    </transition>
</template>

<script>
    export default {
        name: 'BaseSnackbar',
        props: {
            color: {
                type: String,
                default: 'primary'
            },
            textColor: {
                type: String,
                default: null
            },
            timeout: {
                type: Number | String,
                default: 0
            }
        },
        data () {
            return {
                timer: null
            }
        },
        watch: {
            show (value) {
                if (value === true) {
                    if (this.targetTimeout > 0) {
                        this.timer = setTimeout(() => {
                            this.closeSnackbar()
                        }, this.targetTimeout)
                    }
                }
            }
        },
        computed: {
            targetTimeout () {
                console.log(typeof this.timeout, this.timeout instanceof Number)
                if (this.timeout === 'string') {
                    return Number(this.timeout)
                } else if (typeof this.timeout === 'number') {
                    return this.timeout
                } else {
                    return 3000
                }
            },
            targetBackgroundColor () {
                if (this.color === 'primary') {
                    return '#188cff'
                } else if (this.color === 'warning') {
                    return '#ffa21c'
                } else if (this.color === 'confirm') {
                    return '#a3d9ff'
                } else if (this.color === 'error') {
                    return '#ff5f5f'
                } else {
                    return this.color
                }
            },
            targetTextColor () {
                if (this.textColor === 'primary') {
                    return '#ffffff'
                } else if (this.textColor === 'warning') {
                    return '#ffffff'
                } else if (this.textColor === 'confirm') {
                    return '#000000'
                } else if (this.textColor === 'error') {
                    return '#ffffff'
                } else {
                    return this.textColor
                }
            },
            show () {
                return this.$attrs.value
            }
        },
        methods: {
            closeSnackbar () {
                if (this.timer !== null) {
                    clearTimeout(this.timer)
                }
                this.timer = null
                this.$emit('input', false)
            }
        }
    }
</script>

<style scoped>
    .base-snackbar-wrapper {
        position: fixed;
        z-index: 10000;
        bottom: 0;
        width: 100%;
        min-height: 180px;
        border-radius: 15px;
        padding: 40px 100px 100px 40px;
    }

    .base-snackbar-text {
        font-size: 42px;
        color: white;
    }

    .base-snackbar-contents-wrapper {
        position: relative;
    }

    .base-snackbar-close-wrapper {
        position: absolute;
        right: 40px;
        bottom: 40px;
    }

    .slide-enter-active, .slide-leave-active {
        transition: margin-bottom .3s cubic-bezier(1,.015,.295,1.225);
    }

    .slide-enter, .slide-leave-to {
        margin-bottom: -100%;
    }

    .slide-enter-to, .slide-leave {
        margin-bottom: 0px;
    }
</style>
