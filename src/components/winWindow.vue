<template lang="pug">
.win.flex-column.cap(ref="winWindow")
    p.special-font well done!
    p 
        | you finished in about 
        span.time(ref="winTime")
        | s
    .results
        p.title.cap.my-10.py-10 the results
        .parent.border(ref="resultsParent")
    button.btn.cap(type="button", @click="$emit('again')") play again
</template>
<script>
export default {
    methods: {
        win() {
            let timerSpan = document.querySelector(".timer .time"), 
                winWindow = this.$refs.winWindow,
                addition = winWindow.scrollHeight - window.innerHeight,
                container = document.querySelector(".container");
            this.$refs.winTime.textContent = timerSpan.dataset.time - timerSpan.textContent;
            this.setToLocalStorage();
            this.arrangeInfo();
            this.showResults();
            this.$emit("sound", "win");
            if (addition > 0) {
               container.style.height = `${container.scrollHeight + addition + 250}px`;
            }
            winWindow.classList.add("show");
        },
        setToLocalStorage() {
            let nameSpan = document.querySelector(".header .name"),
                winTime = this.$refs.winTime,
                playerInfo = {
                    name: nameSpan.textContent,
                    time: winTime.textContent,
                },
                playersInfo = JSON.parse(
                    window.localStorage.getItem("players")
                ),
                j = 0;
            if (!playersInfo) {
                window.localStorage.setItem(
                    "players",
                    JSON.stringify([playerInfo])
                );
            } else {
                if (!Array.isArray(playersInfo)) {
                    playersInfo = [playersInfo];
                }
                for (let i = 0; i < playersInfo.length; i++) {
                    if (playersInfo[i].name == nameSpan.textContent) {
                        if (playersInfo[i].time > winTime.textContent) {
                            playerInfo.time = winTime.textContent;
                            playersInfo.splice(i, 1);
                            playersInfo.push(playerInfo);
                        }
                        j++;
                    }
                }
                if (j == 0) {
                    playersInfo.push(playerInfo);
                }
                window.localStorage.setItem(
                    "players",
                    JSON.stringify(playersInfo)
                );
            }
        },
        arrangeInfo() {
            let playersInfo = JSON.parse(
                    window.localStorage.getItem("players")
                ),
                playersTime = [1],
                playersNames = [1],
                arrangingTime = [1],
                arrangingInfo = [1];
            for (let i = 0; i < playersInfo.length; i++) {
                if (i == 0) {
                    playersTime = [playersInfo[i].time];
                    playersNames = [playersInfo[i].name];
                } else {
                    playersTime.push(playersInfo[i].time);
                    playersNames.push(playersInfo[i].name);
                }
            }
            playersTime = playersTime.map((time) => +time);
            for (let i = 0; i < playersInfo.length; i++) {
                let minValue = Math.min(...playersTime),
                    indexOfMinValue = playersTime.indexOf(+minValue);
                if (i == 0) {
                    arrangingTime = [minValue];
                } else {
                    arrangingTime.push(minValue);
                }
                playersTime.splice(indexOfMinValue, 1);
            }
            playersInfo.forEach((obj) => {
                obj.taken = false;
            });
            for (let i = 0; i < playersInfo.length; i++) {
                for (let j = 0; j < playersInfo.length; j++) {
                    if (playersInfo[j].time == arrangingTime[i]) {
                        if (!playersInfo[j].taken) {
                            if (i == 0) {
                                arrangingInfo = [playersInfo[j]];
                            } else {
                                arrangingInfo.push(playersInfo[j]);
                            }
                            playersInfo[j].taken = true;
                            break;
                        }
                    }
                }
            }
            window.localStorage.setItem(
                "players",
                JSON.stringify(arrangingInfo)
            );
        },
        showResults() {
            let playersInfo = JSON.parse(
                window.localStorage.getItem("players")
            ),
                resultsParent = this.$refs.resultsParent,
                container = document.querySelector(".container");
            resultsParent.innerHTML = "";
            for (let i = 0; i < playersInfo.length; i++) {
                let playerInfo = playersInfo[i],
                    parent = document.createElement("div"),
                    text = document.createElement("div"),
                    num = document.createElement("span"),
                    name = document.createElement("div"),
                    winTime = document.createElement("span"),
                    time = document.createElement("span"),
                    button = document.createElement("button");
                parent.classList.add("result");
                text.classList.add("text");
                num.classList.add("num");
                num.textContent = i + 1;
                text.appendChild(num);
                name.classList.add("name");
                name.textContent = playerInfo.name;
                text.appendChild(name);
                winTime.classList.add("win-time");
                time.classList.add("time");
                time.textContent = playerInfo.time;
                winTime.appendChild(time);
                winTime.append("s");
                text.appendChild(winTime);
                parent.appendChild(text);
                button.textContent = "remove";
                button.onclick = () => {
                    parent.remove();
                    playersInfo.splice(i, 1);
                    window.localStorage.setItem(
                        "players",
                        JSON.stringify(playersInfo)
                    );
                    this.showResults();
                    if (container.scrollHeight >= window.innerHeight + 30) {
                        container.style.height = `${
                            container.scrollHeight - 30
                        }px`;
                    }
                };
                parent.appendChild(button);
                resultsParent.appendChild(parent);
            }
        },
    }
}
</script>