<template>
    <div class="custom-range">
        <div class="custom-range__data">
            <input type="text" class="custom-range__value" :value="value" @input="$emit('input', $event.target.value)">
            <span>%</span>
        </div>
        <div class="custom-range__body" @click="drag(true, true)">
            <div class="custom-range__circle" @mousedown="drag(true)" @touchstart="drag(true)" :style="{ left: leftDrag + 'px' }"></div>
        </div>
        <div class="custom-range__buttons">
            <div class="custom-range__button" v-for="(value,index) in buttons" :key="index" @click="clickLeft(value)">
                {{ value }}%
            </div>
        </div>
    </div>
</template>

<script>
    export default {
        name: "custom-range",
        data() {
            return {
                buttons: [25,50,75,100],
                is_draggable: false,
                leftDrag: 0,
            }
        },
        props: ['value'],
        watch: {
            value() {
                if (this.value < 0) {
                    this.leftDrag = 0;
                    return;
                } else if (this.value > 100) {
                    this.leftDrag = 100;
                    return;
                }
                this.leftDrag = this.getLeft(false, this.value);
            }
        },
        methods: {
            drag(draggable, click = false) {
                this.is_draggable = draggable;
                if (draggable) {
                    this.move();
                }
                if (click) {
                    this.is_draggable = false;
                }
            },
            move() {
                if (this.is_draggable) {
                    let data;
                    if (event.changedTouches !== undefined) {
                        data= this.getLeft(event.changedTouches[0].clientX);
                    } else {
                        data = this.getLeft(event.clientX);
                    }
                    this.leftDrag = data[0];
                    this.$emit('input', data[1]);
                }
            },
            getLeft(left, percent = false) {
                let newLeft = left;
                let circle = document.querySelector('.custom-range__circle');
                let body = document.querySelector('.custom-range__body');
                if (circle && body) {
                    let width = body.clientWidth - circle.clientWidth - circle.clientWidth / 2 - 2;
                    newLeft = newLeft - circle.clientWidth - circle.clientWidth / 2;
                    if (percent) {
                        return (width / 100 * percent).toFixed(1);
                    }
                    if (newLeft > 0 && newLeft < width) {
                        return [newLeft, (newLeft / width * 100).toFixed(1)];
                    } else if (newLeft <= 0) {
                        return [0, 0];
                    } else if (newLeft >= width) {
                        return [width, 100];
                    }
                }

            },
            clickLeft(value) {
                this.leftDrag = this.getLeft(false, value);
                this.$emit('input', value);
            }
        },
        created() {
            document.addEventListener('mouseup', () => {
                this.drag(false);
            });
            document.addEventListener('touchend', () => {
                this.drag(false);
            });
            document.addEventListener('mousemove', () => {
                this.move();
            });
            document.addEventListener('touchmove', () => {
                this.move();
            });
        },
        mounted() {
            if (this.value !== undefined) {
                this.leftDrag = this.getLeft(false, this.value);
            }
        }
    }
</script>

<style scoped>
    .custom-range {
        background: #23283e;
        padding: 10px;
        font-family: Tahoma;
        margin: 0 auto;
    }
    .custom-range__data {
        display: flex;
        width: 100%;
        color: #6e7389;
        justify-content: flex-start;
        align-items: center;
        font-size: 17px;
    }
    .custom-range__value {
        color: #6e7389;
        background: transparent;
        border: none;
        padding: 5px;
        font-size: 20px;
        width: 40px;
    }
    .custom-range__value:focus {
        border: none;
        outline: none;
    }
    .custom-range__body {
        background: linear-gradient(to right, #64d9c6, #e3ba6b, #eb726a);
        border-radius: 25px;
        margin-top: 15px;
        padding: 5px 7px 5px 7px;
    }
    .custom-range__circle {
        width: 22px;
        height: 22px;
        position: relative;
        background: white;
        border-radius: 50%;
    }
    .custom-range__circle:hover {
        cursor: pointer;
    }
    .custom-range__buttons {
        display: flex;
        justify-content: space-around;
        align-items: center;
        max-width: 70%;
        margin: 20px auto;
    }
    .custom-range__button {
        padding: 5px 25px 5px 25px;
        background: #535f8d;
        color: white;
        border-radius: 25px;
        font-size: 17px;
        font-weight: bolder;
    }
    .custom-range__button:hover {
        cursor: pointer;
    }
    @media (max-width: 600px) {
        .custom-range__buttons {
            max-width: 100%;
        }
    }
    @media (max-width: 400px) {
        .custom-range__buttons {
            flex-direction: column;
        }
        .custom-range__button {
            margin-top: 20px;
        }
    }
</style>