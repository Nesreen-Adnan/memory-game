<template lang="pug">
.game.relative
    .container.flex-column.p-10.relative(ref="container")
        startWindow(@timer="startTimer", @sound="sound")
        stopWindow(ref="stop", @sound="sound", @again="again")
        winWindow(ref="win", @sound="sound", @again="again")
        .header.p-10.cap.border
            .greeting
                | hello: 
                span.name(ref="nameSpan") unknown
            .wrong-tries 
                | wrong tries: 
                span.wrongs(ref="wrongs") 0
        .cards.py-10(data-opened=0, ref="cards")
        .timer.border.p-10
            | timer: 00:
            span.time(data-time=59, ref="timerSpan")
</template>
<script>
import startWindow from "./components/startWindow.vue";
import stopWindow from "./components/stopWindow.vue";
import winWindow from "./components/winWindow.vue";
export default {
    data() {
        return {
            audios: {
                click: "./assets/sounds/click.mp3",
                stop: "./assets/sounds/bottles.mp3",
                button: "./assets/sounds/click-button.mp3",
                begain: "./assets/sounds/begain.mp3",
                win: "./assets/sounds/win.mp3",
                correct: "./assets/sounds/correct.mp3",
            },
        };
    },
    methods: {
        sound(value) {
            let audio = new Audio(this.audios[value]);
            audio.play();
        },
        startTimer() {
            let timerSpan = this.$refs.timerSpan,
               winWindow = document.querySelector(".win"),
               i = 9;
            timerSpan.textContent = timerSpan.dataset.time;
            let timer = setInterval(() => {
               if (!winWindow.classList.contains("show")) {
                   if (timerSpan.textContent > 0) {
                       if (timerSpan.textContent > 10) {
                           timerSpan.textContent--;
                       } else {
                           timerSpan.textContent = `0${i--}`;
                       }
                   } else {
                       clearInterval(timer);
                       this.$refs.stop.stop();
                   }
               }
            }, 1000);
        },
        rotation() {
            let cards = this.$refs.cards;
            [...cards.children].forEach((card) => {
                card.addEventListener("click", () => {
                    if (cards.dataset.opened < 2) {
                        card.classList.add("rotation");
                        cards.dataset.opened++;
                        this.sound("click");
                    }
                    if (cards.dataset.opened == 2) {
                        [...cards.children].forEach((el) => {
                            if (
                                el.dataset.src == card.dataset.src &&
                                el.dataset.num != card.dataset.num &&
                                el.classList.contains("rotation")
                            ) {
                                card.classList.add("correct");
                                el.classList.add("correct");
                                this.sound("correct");
                            }
                        });
                        if (!card.classList.contains("correct")) {
                            this.$refs.wrongs.textContent++;
                        }
                        setTimeout(() => {
                            this.removeAll("rotation", [...cards.children]);
                            cards.dataset.opened = 0;
                        }, 1200);
                    }
                    this.checkwin();
                });
            });
        },
        createCards() {
            this.$refs.cards.innerHTML = "";
            let imagesSources = [
                    "https://cdn.worldvectorlogo.com/logos/html-1.svg",
                    "https://cdn.worldvectorlogo.com/logos/typescript.svg",
                    "https://cdn.worldvectorlogo.com/logos/css-3.svg",
                    "https://cdn.worldvectorlogo.com/logos/vue-9.svg",
                    "https://cdn.worldvectorlogo.com/logos/gulp.svg",
                    "https://cdn.worldvectorlogo.com/logos/sass-1.svg",
                    "https://cdn.worldvectorlogo.com/logos/react-2.svg",
                    "https://cdn.worldvectorlogo.com/logos/java-14.svg",
                    "https://cdn.worldvectorlogo.com/logos/c.svg",
                    "https://cdn.worldvectorlogo.com/logos/c--4.svg",
                    "https://cdn.worldvectorlogo.com/logos/bootstrap-5-1.svg",
                ],
                newArr = [...imagesSources, ...imagesSources],
                total = newArr.length;
            for (let i = 0; i < total; i++) {
                let index = Math.floor(Math.random() * (total - i)),
                    card = document.createElement("div"),
                    frontface = document.createElement("div"),
                    backface = document.createElement("div"),
                    img = document.createElement("img");
                card.classList.add("card", "border", "transition", "relative");
                card.dataset.num = i;
                card.dataset.src = newArr[index];
                card.dataset.soundonclick = "click";
                frontface.classList.add("frontface", "flex", "abs");
                frontface.textContent = "?";
                card.appendChild(frontface);
                backface.classList.add("backface", "flex", "abs");
                img.src = `${newArr[index]}`;
                backface.appendChild(img);
                card.appendChild(backface);
                this.$refs.cards.appendChild(card);
                newArr.splice(index, 1);
            }
            this.rotation();
        },
        checkwin() {
            let j = 0,
                cards = this.$refs.cards;
            [...cards.children].forEach((card) => {
                if (card.classList.contains("correct")) {
                    j++;
                }
            });
            if (j == cards.children.length) {
                this.$refs.win.win();
            }
        },
        again() {
            let timerSpan = this.$refs.timerSpan,
                stopWindow = document.querySelector(".stop"),
                winWindow = document.querySelector(".win");
            this.$refs.container.style.height = `${window.innerHeight}px`;
            this.removeAll(["rotation", "correct"], [...this.$refs.cards.children]);
            stopWindow.classList.remove("show");
            winWindow.classList.remove("show");
            this.$refs.wrongs.textContent = 0;
            this.createCards();
            timerSpan.textContent = timerSpan.dataset.time;
            this.startTimer();
        },
        //global method
        removeAll(className, elements) {
            elements.forEach((el) => {
                el.classList.remove(className);
            });
        },
    },
    mounted() {
        let buttons = [...document.querySelectorAll(".btn")];
        buttons.forEach((btn) =>
            btn.addEventListener("click", () => this.sound("button"))
        );
        this.createCards();
    },
    components: {
        startWindow,
        stopWindow,
        winWindow,
    },
};
</script>
<style lang="scss">
//fonts
@import url("https://fonts.googleapis.com/css2?family=Tilt+Prism&display=swap");
@import url("https://fonts.googleapis.com/css2?family=Open+Sans:wght@400;700&display=swap");
#app {
    font-family: "Open Sans", sans-serif;
    -webkit-font-smoothing: antialiased;
    -moz-osx-font-smoothing: grayscale;
    perspective: 700px;
    .special-font {
        font-family: "Tilt Prism", cursive;
    }
}
.cap {
    text-transform: capitalize;
}
.up {
    text-transform: uppercase;
}
.normal {
    text-transform: none;
}
.mx-10 {
    margin-left: 10px;
    margin-right: 10px;
}
.my-10 {
    margin-bottom: 10px;
    margin-top: 10px;
}
.ml-10 {
    margin-left: 10px;
}
.mr-10 {
    margin-right: 10px;
}
.m-10 {
    margin: 10px;
}
.px-10 {
    padding-left: 10px;
    padding-right: 10px;
}
.py-10 {
    padding-bottom: 10px;
    padding-top: 10px;
}
.p-10 {
    padding: 10px;
}
@mixin flex($justify: center, $align: center, $gap: 10) {
    display: flex;
    justify-content: $justify;
    align-items: $align;
    gap: 10px;
}
.flex {
    @include flex;
}
.flex-wrap {
    @include flex();
    flex-wrap: wrap;
}
.flex-column {
    @include flex();
    flex-direction: column;
}
@mixin abs($left: 0, $top: 0, $width: 100%, $height: 100%) {
    position: absolute;
    left: $left;
    top: $top;
    width: $width;
    height: $height;
}
.abs {
    @include abs;
}
.relative {
    position: relative;
}
.transition {
    transition: 0.3s;
}
:root {
    --mainColor: rgb(126, 140, 17);
    --redColor: rgb(140, 41, 32);
    --lightRed: rgb(242, 92, 5);
    --brownColor: #d9910d;
    --darkBrown: rgb(183, 122, 8);
    --greenColor: rgb(6, 173, 191);
}
* {
    box-sizing: border-box;
    margin: 0;
    padding: 0;
}
.border {
    border: 3px solid var(--darkBrown);
}
.container {
    margin-left: auto;
    margin-right: auto;
    height: 100%;
    min-height: 100vh;
    overflow-y: hidden;
}
.container {
    $backgroundOpacity: 95%;
    justify-content: space-between;
    > * {
        width: 100%;
    }
    .start,
    .stop,
    .win {
        z-index: 99;
        transition: 0.8s;
        color: #fff;
        > button {
            margin-top: 30px;
            padding: 15px 30px;
        }
        button {
            background-color: var(--brownColor);
            color: #fff;
            font-weight: bold;
            border: none;
            cursor: pointer;
            font-size: 19px;
            transition: background-color 0.3s;
            &:hover {
                background-color: var(--lightRed);
            }
        }
        .special-font {
            letter-spacing: 4px;
            font-size: 45px;
            margin-bottom: 20px;
        }
    }
    .stop,
    .win {
        @include abs(-100%, 0);
        &.show {
            transform: translate(100%, 0);
        }
        > p:first-child:not(.special-font) {
            font-size: 30px;
            font-weight: bold;
            margin-bottom: 40px;
            letter-spacing: 3px;
        }
        > p {
            font-size: 18px;
            margin: 10px 0;
        }
    }
    .start {
        background-color: rgba(126, 140, 17, $backgroundOpacity);
        .get-name {
            $mainColor: var(--brownColor);
            @include abs(-500px, 50%);
            background-color: rgba(75, 84, 8, 0.8);
            text-align: center;
            padding: 25px 20px;
            width: calc(100% - 80px);
            max-width: 500px;
            height: fit-content;
            border: 7px double $mainColor;
            transition: 0.8s;
            transform: translate(0, -50%);
            &.show {
                left: 50%;
                transform: translate(-50%, -50%);
            }
            label {
                font-size: 20px;
            }
            input {
                display: block;
                padding: 10px;
                margin: 18px 0;
                border: none;
                width: 100%;
                caret-color: $mainColor;
                color: var(--darkBrown);
                font-size: 18px;
                &:focus {
                    outline: none;
                }
            }
            button {
                padding: 10px 25px;
                font-weight: 400;
                font-size: 18px;
            }
        }
        &.hidden {
            transform: translateX(-100%);
        }
    }
    .win {
        background-color: rgba(6, 173, 191, $backgroundOpacity);
        .results {
            color: #fff;
            .title {
                font-size: 18px;
                text-align: center;
                font-weight: bold;
                color: var(--brownColor);
                background-color: #fff;
            }
            .parent {
                border-color: #fff;
                .result {
                    padding: 10px;
                    @include flex(space-between, center, 0);
                    text-transform: capitalize;
                    .text {
                        @include flex(flex-start);
                        width: 100%;
                        .num {
                            $numSize: 20px;
                            @include flex();
                            background-color: #fff;
                            color: var(--greenColor);
                            border-radius: 50%;
                            font-weight: bold;
                            width: $numSize;
                            height: $numSize;
                        }
                        .win-time {
                            margin-left: auto;
                        }
                    }
                    button {
                        font-size: 14px;
                        font-weight: 400;
                        margin-left: 10px;
                        padding: 10px;
                        text-transform: capitalize;
                    }
                }
            }
        }
    }
    .stop {
        background-color: rgba(140, 41, 32, $backgroundOpacity);
    }
    .header {
        @include flex(space-between);
    }
    .cards {
        $cardSize: 100px;
        display: grid;
        grid-template-columns: repeat(auto-fit, minmax($cardSize, 1fr));
        gap: 20px 3px;
        .card {
            width: $cardSize;
            height: $cardSize;
            transition: 1.3s;
            cursor: pointer;
            .frontface {
                background-color: var(--brownColor);
                font-size: 50px;
                font-weight: bold;
                color: #fff;
                z-index: 3;
                backface-visibility: hidden;
            }
            .backface {
                transform: rotateY(-180deg);
                z-index: -1;
                background-color: var(--brownColor);
                img {
                    width: calc($cardSize - 20px);
                    max-height: 100%;
                }
            }
            &.rotation,
            &.correct {
                pointer-events: none;
                transform: rotateY(180deg);
                .frontface {
                    transform: rotateY(180deg);
                }
            }
        }
    }
}
</style>
