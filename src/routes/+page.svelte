<script>
    import { onMount } from "svelte";
	import { injectSpeedInsights } from '@vercel/speed-insights/sveltekit';
	injectSpeedInsights();
	import '../styles/layout.css'
	import BGScreenSaver from '../lib/bg_screensaver.svelte'
	let mode = 'focus'; // focus, short, long
	

	let setF, setL, setS, bar;
	onMount(() => {
		setF = document.getElementById('setFocus');
		setL = document.getElementById('setLong');
		setS = document.getElementById('setShort');
        progressBar = document.getElementById('progressBar');
	});
    

	let setBG = (FColor, SColor, LColor) => {
		console.log('Changing BG Color');
		if (FColor == true) {
			setF.style.backgroundColor = '#FFFFFF';
			setF.style.color = '#000000';
		} else {
			setF.style.backgroundColor = '#000000';
			setF.style.color = '#FFFFFF';
		}

		if (SColor == true) {
			setS.style.backgroundColor = '#FFFFFF';
			setS.style.color = '#000000';
		} else {
			setS.style.backgroundColor = '#000000';
			setS.style.color = '#FFFFFF';
		}

		if (LColor == true) {
			setL.style.backgroundColor = '#FFFFFF';
			setL.style.color = '#000000';
		} else {
			setL.style.backgroundColor = '#000000';
			setL.style.color = '#FFFFFF';
		}
	};

	let timer = 25*60; // 25 minutes in seconds
    let modeTimer = 25*60
	let timerMinutes = Math.floor(timer / 60);
	let timerSeconds = timer % 60;

	let TimerButton = 'START';

	if (timerSeconds % 60 == 0) {
		timerSeconds = '00';
	} else if (timerSeconds < 10) {
		timerSeconds = `0${timerSeconds}`;
	}

    

	let timerDisplay = `${timerMinutes}:${timerSeconds}`;
	let isRunning = false;
	let intervalId;

	const setMode = (newMode) => {
		if (newMode == mode) {
			return;
		} else {
			mode = newMode;
            updateBar('reset')
			if (mode == 'focus') {
				timer = 25 * 60;
                modeTimer = 25*60
				setBG(true, false, false);
			} else if (mode == 'short') {
				timer = 5 * 60;
                modeTimer = 5*60
				setBG(false, true, false);
			} else if (mode == 'long') {
				timer = 15 * 60;
                modeTimer = 15*60
				setBG(false, false, true);
			}
            clearInterval(intervalId);
		    TimerButton = 'START';
		    intervalId = null;
		    isRunning = false;
            updateDisplay();
		}
	};

	const updateDisplay = () => {
		timerMinutes = Math.floor(timer / 60);
		timerSeconds = timer % 60;

		if (timerSeconds === 0) {
			timerSeconds = '00';
		} else if (timerSeconds < 10) {
			timerSeconds = `0${timerSeconds}`;
		}

		timerDisplay = `${timerMinutes}:${timerSeconds}`;

	};

    const updateBar = (opt) => {
		console.log('updating bar')
        if (opt == 'reset') {
            progressBar.style.transform = `translateY(90vh) translateX(-100vw)`
            return;
        } else {
            progressBar.style.transform = `translateY(90vh) translateX(-${(timer/modeTimer)*100}vw)`
            console.log(timer/modeTimer)
        }
    }

	const stopTimer = () => {
		clearInterval(intervalId);
		TimerButton = 'START';
		intervalId = null;
		isRunning = false;
	};

	let stpsTimer = () => {
		if (isRunning) {
			console.log('Pause');
			stopTimer();
			return;
		} else {
			isRunning = true;
			TimerButton = 'PAUSE';
		}
		if (intervalId) {
			return;
		}

		intervalId = setInterval(() => {
			if (timer > 0) {
				timer--;
				updateDisplay();
                updateBar()
			} else {
				clearInterval(intervalId);
				isRunning = false;
				console.log('Timer finished');
			} 
		}, 1000);
	};
</script>
<div id="mainDiv">
	<BGScreenSaver />
    <h1 id="title">POMO-NOIR</h1>
	<div id="Clock">
		<div id="Menu">
			<button id="setFocus" on:click={() => setMode('focus')} class="menuButton bghv-grey">Focus</button>
			<button id="setShort" on:click={() => setMode('short')} class="menuButton bghv-grey"
				>Short Break</button
			>
			<button id="setLong" on:click={() => setMode('long')} class="menuButton bghv-grey"
				>Long Break</button
			>
		</div>
		<h1 id="Timer">{timerDisplay}</h1>
		<button on:click={stpsTimer} id="TimerButton" class="bghv-grey">
			{TimerButton}
		</button>
	</div>
	<div id="progressBar">
        <hr id="bar">
    </div>
</div>
