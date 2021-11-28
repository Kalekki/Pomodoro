<script>
import { bind } from "svelte/internal";

let focustime = 25;
let shortbreak = 5; // 300seconds = 5min
let longbreak = 15; // 3*shortbreak = 900seconds = 15min
let cycles = 4; // number of short breaks before long break
let currentState = "Focus time"
let time = focustime * 60;
let isbreak = false;
let pause = true;
let autopause = true;
let startbuttonText = "Start timer";
let initialPress = true;
let finished = false;
let alarm = new Audio('alarm.ogg');
let silence = new Audio('silence.ogg');
$: formatTime = secondsToMinutes(time);

function startTimer(duration) {
    pause = !pause;
    if (pause) {
        if(!finished) {
            startbuttonText = "Resume";
        }
        else {
            startbuttonText = "Start timer";
        }
    } 
    else {
        if(!finished) {
            startbuttonText = "Pause";
        }
        else {
            currentState = "Focus time";
            finished = false;
            startbuttonText = "Pause";

        }
    }
    if (initialPress){
        time = Math.round(focustime*60);
        setInterval(function () {
            initialPress = false;
            if (!pause) time = time - 1;
            if (time == 0) {
                if(autopause){pause = !pause;
                startbuttonText = "Start";
                }
                alarm.volume = 0.5;
                alarm.play();
                if (!isbreak) {
                    isbreak = true;
                    if (cycles == 1) {
                        currentState = "Long break";
                        time = Math.round(longbreak * 60);
                        cycles = 0;

                        isbreak = false;
                    } else if (cycles > 1) {
                        currentState = "Short break";
                        time = Math.round(shortbreak * 60);
                        cycles = cycles - 1;
                    } else {
                        pause = true
                        currentState = "You have completed your work session!";
                        time = Math.round(focustime * 60)
                        startbuttonText = "Restart!";
                        finished = true
                        cycles = 4
                    }
                } else {
                    isbreak = false;
                    currentState = "Focus time";
                    time = Math.round(focustime * 60);
                }
            }else{
                // little audio hack to keep the tab from pausing in background
                silence.play();
            }
    }, 1000);
    }
};

function secondsToMinutes(sec) {
    let minutes = Math.floor(sec / 60);
    let seconds = sec % 60;
    return minutes.toString().padStart(2, '0') + ':' + seconds.toString().padStart(2, '0');
};

</script>
<style>
    input::-webkit-outer-spin-button,
    input::-webkit-inner-spin-button {
    -webkit-appearance: none;
    margin: 0;
    }

    /* Firefox */
    input[type=number] {
    -moz-appearance: textfield;
    }
</style>
<svelte:head>
    <title>{secondsToMinutes(time)}</title>
</svelte:head>

<main class="bg-gray-600 rounded-b-xl border-b-4 border-solid border-gray-900 filter: drop-shadow-2xl">
    <h1 class="text-5xl text-white py-10" >{currentState}</h1>
    <h2 class="text-7xl text-white filter drop-shadow-lg">{secondsToMinutes(time)}</h2>
    <button class="
        bg-red-500
        text-white
        active:bg-purple-600
        font-bold
        uppercase
        text-md
        mt-10
        mb-10
        px-6
        py-3
        rounded-full
        shadow
        hover:shadow-lg
        outline-none
        focus:outline-none
        mr-1
        mb-1
        ease-linear
        transition-all
        duration-150"
     on:click={startTimer(time)}>{startbuttonText}</button>
<!--  SETTINGS  -->
<label class="text-white text-xl"><input type="checkbox" class="" bind:checked={autopause}> Autopause</label>
    <div class="grid grid-cols-3 gap-2 flex flex-wrap place-items-center py-5 mx-20">
        <div class="flex center"> <!-- Focus time slider -->
            <label class="text-white text-xl">
                <input type="number" class="w-8 text-center bg-gray-700" bind:value={focustime} min=10 max=60>
                <input type="range" class="" bind:value={focustime} min=10 max=60>
                <p>Focus time length (minutes)</p>
            </label>
        </div>
        <label class="text-white text-xl">
            <input type="number" class="w-8 text-center bg-gray-700" bind:value={shortbreak} min=2 max=20>
            <input type="range" bind:value={shortbreak} min=2 max=20>
            <p>Short break length (minutes)</p>
        </label>
        <label class="text-white text-xl">
            <input type="number" class="w-8 text-center bg-gray-700" bind:value={longbreak} min=5 max=60>
            <input type="range" bind:value={longbreak} min=5 max=60>
            <p>Long break length (minutes)</p>
        </label>
    </div>
</main>
