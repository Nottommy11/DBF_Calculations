<script>
	// import { Canvas } from '@threlte/core';
	// import Scene from '$lib/components/Scene.svelte';
	
	import Slider from '@bulatdashiev/svelte-slider';
	
	// DO NOT ADJUST
	const duckWidth = 2.3;
	const duckLength = 2.5
	const duckHeight = 2.5;
	const duckWeight = 0.7;

	// DO NOT ADJUST
	const puckDiameter = 3;
	const puckThickness = 1;
	const puckWeight = 6;

	// DO NOT ADJUST
	const ip1 = 6;
	const ip2 = 2;
	const ic1 = 10;
	const ic2 = 8;
	const ce = 10;
	const cp = 0.5;
	const cc = 2;

	// DO NOT ADJUST
	const m2MinPassengerCnt = 3;
	const m2MinCargoCnt = 1;
	const m2PassToCargoRatio = 3;

	// CAN ADJUST TO REASONABLE NUMBERS
	const m2MaxLaps = 8;
	const m2MaxPassengerCnt = 102;
	const m2MaxCargoCnt = m2MaxPassengerCnt/3;
	const m2MinBatCap = 1;
	const m2MaxBatCap = 100;

	// CAN ADJUST TO REASONABLE NUMBERS
	const m3MaxLaps = 8;
	const m3BannerMaxHeight = 25;

	// DO NOT ADJUST
	const m3MinRAC = 0.9;
	const m3MinWingspan = parseFloat((12*((m3MinRAC-0.75)/0.05)).toFixed(2));
	const m3MaxWingspan = 60;
	const m3BannerMaxRatio = 5;
	const m3BannerMinHeight = 2;
	const m3BannerMinLength = m3BannerMaxRatio*m3BannerMinHeight;
	const m3BannerMaxLength = m3BannerMaxRatio*m3BannerMaxHeight;

	// CAN ADJUST TO REASONABLE NUMBERS
	const minGMTime = 1;
	const maxGMTime = 420;

	// NEED TO ADJUST TO REASONABLE NUMBERS
	const baseMaxNetIncome = 1;
	const baseMaxBannerScore = 1;
	const baseMinMissionTime = 1;

	// WILL BE CALCULATED
	let totalScore = 0;
	let m1Score = [0,1];
	let m2Score = 0;
	let m3Score = 0;
	let gmScore = 0;

	// VARIABLES TO PLAY WITH
	let proposalScore = [0,100];
	let designReportScore = [0,100];
	let participationScore = [0,3];

	// WILL BE CALCULATED
	let maxNetIncome = baseMaxNetIncome;
	let maxBannerScore = baseMaxBannerScore;
	let minMissionTime = baseMinMissionTime;

	// VARIABLES TO PLAY WITH
	let m2Laps = [0,m2MaxLaps];
	let m2PassengerCnt = [m2MinPassengerCnt,m2MaxPassengerCnt];
	let m2CargoCnt = [m2MinCargoCnt,m2MaxCargoCnt];
	let m2BatCap = [m2MinBatCap,m2MaxBatCap];

	// VARIABLES TO PLAY WITH
	let m3Laps = [0,m3MaxLaps];
	let m3Wingspan = [m3MinWingspan,m3MaxWingspan];
	let m3BannerHeight = [m3BannerMinHeight,m3BannerMaxHeight];
	let m3BannerLength = [m3BannerMinLength,m3BannerMaxLength];

	// VARIABLES TO PLAY WITH
	let gmTime = [minGMTime,maxGMTime];

	function calcTotalScore(){
		return calcTotalReportScore()*calcMissionScore()+participationScore[0];
	}

	function calcTotalReportScore(){
		return 0.15*proposalScore[0]+0.85*designReportScore[0];
	}

	function calcMissionScore(){
		return m1Score[0]+calcM2Score()+calcM3Score()+calcGMScore();
	}

	function calcM2Score(){
		return 1+(calcNetIncome()/calcMaxNetIncome());
	}

	function calcNetIncome(){
		return calcIncome()-calcCost();
	}

	function calcIncome(){
		return (m2PassengerCnt[0]*(ip1+(ip2*m2Laps[0])))+(m2CargoCnt[0]*(ic1+(ic2*m2Laps[0])));
	}

	function calcCost(){
		return (m2Laps[0]*(ce+(m2PassengerCnt[0]*cp)+(m2CargoCnt[0]*cc))*calcEfficiencyFactor());
	}

	function calcEfficiencyFactor(){
		return m2BatCap[0]/100;
	}

	function calcM3Score(){
		return 2+(calcBannerScore()/calcMaxBannerScore());
	}

	function calcBannerScore(){
		return m3Laps[0]*m3BannerLength[0]/calcRAC();
	}

	function calcRAC(){
		return 0.05*m3Wingspan[0]+0.75;
	}

	function calcBannerRatio(){
		return m3BannerLength[0]/m3BannerHeight[0];
	}

	function calcGMScore(){
		return gmTime[0] === 0 ? 0 :calcMinMissionTime()/gmTime[0];
	}

	function calcPassToCargoRatio(){
		return m2PassengerCnt[0]/m2CargoCnt[0];
	}

	function calcReqPassArea(){
		return m2PassengerCnt[0]*(duckWidth*duckLength);
	}

	function calcPassWeight(){
		return parseFloat((m2PassengerCnt[0]*duckWeight).toFixed(2));
	}

	function calcReqCargoArea(){
		return m2CargoCnt[0]*(puckDiameter*puckDiameter);
	}

	function calcCargoWeight(){
		return parseFloat((m2CargoCnt[0]*puckWeight).toFixed(2));
	}

	function calcMaxCargoCnt(){
		return parseInt(m2PassengerCnt[0]/m2PassToCargoRatio);
	}

	function calcMaxBannerLength(){
		return 5*m3BannerHeight[0];
	}

	function calcMaxNetIncome(){
		resetMaxNetIncome();
		adjustMaxNetIncome();
		return maxNetIncome;
	}

	function adjustMaxNetIncome(){
		maxNetIncome = calcNetIncome() > maxNetIncome ? calcNetIncome() : maxNetIncome;
	}

	function resetMaxNetIncome(){
		maxNetIncome = baseMaxNetIncome;
	}

	function calcMaxBannerScore(){
		resetMaxBannerScore();
		adjustMaxBannerScore();
		return maxBannerScore;
	}

	function adjustMaxBannerScore(){
		maxBannerScore = calcBannerScore() > maxBannerScore ? calcBannerScore() : maxBannerScore;
	}

	function resetMaxBannerScore(){
		maxBannerScore = baseMaxBannerScore;
	}

	function calcMinMissionTime(){
		resetMinMissionTime();
		adjustMinMissionTime();
		return minMissionTime;
	}

	function adjustMinMissionTime(){
		minMissionTime = gmTime[0] < minMissionTime ? gmTime[0] : minMissionTime;
	}

	function resetMinMissionTime(){
		minMissionTime = baseMinMissionTime;
	}
</script>

<div class="main_scores">
	<h1>Total Score: {calcTotalScore()}</h1>
	<h2>Total Report Score: {calcTotalReportScore()}</h2>
	<h2>Missions Score: {calcMissionScore()}</h2>
	<h2>Participation Score: {participationScore[0]}</h2>
</div>

<div class="report_scores">
	<h2>Proposal Score: {proposalScore[0]}</h2>
	<Slider bind:value={proposalScore} />

	<h2>Design Report Score: {designReportScore[0]}</h2>
	<Slider bind:value={designReportScore} />
</div>

<div class="participation_score">
<h2>Participation Score: {participationScore[0]}</h2>
	<Slider max={participationScore[1]} bind:value={participationScore} />
</div>

<div class="mission_1">
	<h2>Mission 1: {m1Score[0]}</h2>
	<Slider max={m1Score[1]} bind:value={m1Score} />
</div>

<div class="mission_2">
	<h2>Mission 2: {calcM2Score()}</h2>
	<h3>Laps: {m2Laps[0]}</h3>
	<Slider max={m2MaxLaps} bind:value={m2Laps} />
	<h3>Passenger Count: {m2PassengerCnt[0]} (Required Area: {calcReqPassArea()} Sq. In.) (Weight: {calcPassWeight()} oz.)</h3>
	<Slider min={m2MinPassengerCnt} max={m2MaxPassengerCnt} bind:value={m2PassengerCnt} />
	<h3>Cargo Count: {m2CargoCnt[0]} (Required Area: {calcReqCargoArea()} Sq. In.) (Weight: {calcCargoWeight()} oz.)</h3>
	{#if calcMaxCargoCnt() === m2MinCargoCnt}
		<div class="greyed-out">
			<Slider max={m2MinCargoCnt} bind:value={m2CargoCnt} />
		</div>
	{:else}
		<Slider min={m2MinCargoCnt} max={calcMaxCargoCnt()} bind:value={m2CargoCnt} />
	{/if}
	<h3>Total Battery Capacity: {m2BatCap[0]} Watt-Hours</h3>
	<Slider min={m2MinBatCap} max={m2MaxBatCap} bind:value={m2BatCap} />
</div>

<div class="mission_3">
	<h2>Mission 3: {calcM3Score()}</h2>
	<h3>Laps: {m3Laps[0]}</h3>
	<Slider max={m3MaxLaps} bind:value={m3Laps} />
	<h3>Wingspan: {m3Wingspan[0]} Inches</h3>
	<Slider min={m3MinWingspan} max={m3MaxWingspan} bind:value={m3Wingspan} />
	<h3>Banner Height: {m3BannerHeight[0]} Inches</h3>
	<Slider min={m3BannerMinHeight} max={m3BannerMaxHeight} bind:value={m3BannerHeight} />
	<h3>Banner Length: {m3BannerLength[0]} Inches</h3>
	{#if calcMaxBannerLength() === m3BannerMinLength}
		<div class="greyed-out">
			<Slider max={m3BannerMinLength} bind:value={m3BannerLength} />
		</div>
	{:else}
		<Slider min={m3BannerMinLength} max={calcMaxBannerLength()} bind:value={m3BannerLength} />
	{/if}
</div>

<div class="ground_mission">
	<h2>Ground Mission: {calcGMScore()}</h2>
	<h3>Mission Time: {gmTime[0]} Seconds</h3>
	<Slider min={minGMTime} max={maxGMTime} bind:value={gmTime} />
</div>

<!--
Custom slot: left: {range[0]}
right: {range[1]}
<div>
	<Slider max="200" step="10" bind:value={range} range on:input={e => console.log(e.detail)}/>
</div>
-->

<!--
<Canvas>
  <Scene />
</Canvas>
-->

<style>
	div.greyed-out {
		--progress-bg: #c0c0c0;
		--track-bg: #808080;
		pointer-events: none;
	}

	div.main_scores {
		background: #f7dafa;
	}

	div.report_scores {
		--progress-bg: #5855ff;
		--track-bg: #fcd7ff;
	}

	div.participation_score {
		--progress-bg: #55b2ff;
		--track-bg: #feffd7;
	}

	div.mission_1 {
		--progress-bg: #d455ff;
		--track-bg: #fffed7;
	}

	div.mission_2 {
		--progress-bg: #5574ff;
		--track-bg: #f1d7ff;
	}

	div.mission_3 {
		--progress-bg: #ffd255;
		--track-bg: #d7e8ff;
	}

	div.ground_mission {
		--progress-bg: #0cb800;
		--track-bg: #ffdad7;
	}
</style>
