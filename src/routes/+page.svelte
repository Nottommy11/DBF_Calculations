<script>
    import ThreeScene from "$lib/ThreeScene.svelte";

    import Slider from "@bulatdashiev/svelte-slider";

    // DO NOT ADJUST
    const duckWidth = 2.3;
    const duckLength = 2.5;
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
    const m2MaxCargoCnt = m2MaxPassengerCnt / 3;
    const m2MinBatCap = 1;
    const m2MaxBatCap = 100;

    // CAN ADJUST TO REASONABLE NUMBERS
    const m3MaxLaps = 8;
    const m3BannerMaxHeight = 25;

    // DO NOT ADJUST
    const m3MinRAC = 0.9;
    const m3MinWingspan = parseFloat(
        (12 * ((m3MinRAC - 0.75) / 0.05)).toFixed(2)
    );
    const m3MaxWingspan = 60;
    const m3BannerMaxRatio = 5;
    const m3BannerMinHeight = 2;
    const m3BannerMinLength = m3BannerMaxRatio * m3BannerMinHeight;
    const m3BannerMaxLength = m3BannerMaxRatio * m3BannerMaxHeight;

    // CAN ADJUST TO REASONABLE NUMBERS
    const minGMTime = 1;
    const maxGMTime = 420;

    // NEED TO ADJUST TO REASONABLE NUMBERS
    const baseMaxNetIncome = 1;
    const baseMaxBannerScore = 1;
    const baseMinMissionTime = 1;

    // WILL BE CALCULATED
    let totalScore = 0;
    let m1Score = [0, 1];
    let m2Score = 0;
    let m3Score = 0;
    let gmScore = 0;

    // SETTINGS TO PLAY WITH
    let useRatio = false;

    // VARIABLES TO PLAY WITH
    let proposalScore = [0, 100];
    let designReportScore = [0, 100];
    let participationScore = [0, 3];

    // WILL BE CALCULATED
    let maxNetIncome = baseMaxNetIncome;
    let maxBannerScore = baseMaxBannerScore;
    let minMissionTime = baseMinMissionTime;

    // VARIABLES TO PLAY WITH
    let m2Laps = [0, m2MaxLaps];
    let m2PassengerCnt = [m2MinPassengerCnt, m2MaxPassengerCnt];
    let m2CargoCnt = [m2MinCargoCnt, m2MaxCargoCnt];
    let m2BatCap = [m2MinBatCap, m2MaxBatCap];

    // VARIABLES TO PLAY WITH
    let m3Laps = [0, m3MaxLaps];
    let m3Wingspan = [m3MinWingspan, m3MaxWingspan];
    let m3BannerHeight = [m3BannerMinHeight, m3BannerMaxHeight];
    let m3BannerLength = [m3BannerMinLength, m3BannerMaxLength];

    // VARIABLES TO PLAY WITH
    let gmTime = [minGMTime, maxGMTime];

    function calcTotalScore() {
        return (
            calcTotalReportScore() * calcMissionScore() + participationScore[0]
        );
    }

    function calcTotalReportScore() {
        return 0.15 * proposalScore[0] + 0.85 * designReportScore[0];
    }

    function calcMissionScore() {
        return m1Score[0] + calcM2Score() + calcM3Score() + calcGMScore();
    }

    function calcM2Score() {
        return useRatio
            ? 1 + calcNetIncome() / calcMaxNetIncome()
            : 1 + calcNetIncome();
    }

    function calcNetIncome() {
        return calcIncome() - calcCost();
    }

    function calcIncome() {
        return (
            m2PassengerCnt[0] * (ip1 + ip2 * m2Laps[0]) +
            m2CargoCnt[0] * (ic1 + ic2 * m2Laps[0])
        );
    }

    function calcCost() {
        return (
            m2Laps[0] *
            (ce + m2PassengerCnt[0] * cp + m2CargoCnt[0] * cc) *
            calcEfficiencyFactor()
        );
    }

    function calcEfficiencyFactor() {
        return m2BatCap[0] / 100;
    }

    function calcM3Score() {
        return useRatio
            ? 2 + calcBannerScore() / calcMaxBannerScore()
            : 2 + calcBannerScore();
    }

    function calcBannerScore() {
        return (m3Laps[0] * m3BannerLength[0]) / calcRAC();
    }

    function calcRAC() {
        return 0.05 * m3Wingspan[0] + 0.75;
    }

    function calcBannerRatio() {
        return m3BannerLength[0] / m3BannerHeight[0];
    }

    function calcGMScore() {
        return gmTime[0] === 0 ? 0 : calcMinMissionTime() / gmTime[0];
    }

    function calcPassToCargoRatio() {
        return m2PassengerCnt[0] / m2CargoCnt[0];
    }

    function calcReqPassArea() {
        return m2PassengerCnt[0] * (duckWidth * duckLength);
    }

    function calcPassWeight() {
        return parseFloat(m2PassengerCnt[0] * duckWeight);
    }

    function calcReqCargoArea() {
        return m2CargoCnt[0] * (puckDiameter * puckDiameter);
    }

    function calcCargoWeight() {
        return parseFloat(m2CargoCnt[0] * puckWeight);
    }

    function calcMaxCargoCnt() {
        return parseInt(m2PassengerCnt[0] / m2PassToCargoRatio);
    }

    function calcMaxBannerLength() {
        return 5 * m3BannerHeight[0];
    }

    function calcMaxNetIncome() {
        resetMaxNetIncome();
        adjustMaxNetIncome();
        return maxNetIncome;
    }

    function adjustMaxNetIncome() {
        maxNetIncome =
            calcNetIncome() > maxNetIncome ? calcNetIncome() : maxNetIncome;
    }

    function resetMaxNetIncome() {
        maxNetIncome = baseMaxNetIncome;
    }

    function calcMaxBannerScore() {
        resetMaxBannerScore();
        adjustMaxBannerScore();
        return maxBannerScore;
    }

    function adjustMaxBannerScore() {
        maxBannerScore =
            calcBannerScore() > maxBannerScore
                ? calcBannerScore()
                : maxBannerScore;
    }

    function resetMaxBannerScore() {
        maxBannerScore = baseMaxBannerScore;
    }

    function calcMinMissionTime() {
        resetMinMissionTime();
        adjustMinMissionTime();
        return minMissionTime;
    }

    function adjustMinMissionTime() {
        minMissionTime =
            gmTime[0] < minMissionTime ? gmTime[0] : minMissionTime;
    }

    function resetMinMissionTime() {
        minMissionTime = baseMinMissionTime;
    }
</script>

<div class="scoring">
    <header class="calculated-scores">
        <h1>Total Score: {calcTotalScore().toFixed(2)}</h1>
        <h2>Total Report Score: {calcTotalReportScore().toFixed(2)}</h2>
        <h2>Missions Score: {calcMissionScore().toFixed(2)}</h2>
        <h2>Participation Score: {participationScore[0]}</h2>
    </header>

    <section class="settings">
        <div class="use-ratio">
            <h2>Use Mission Score Ratios?</h2>
            <label class="toggle">
                <input type="checkbox" id="switch" bind:checked={useRatio} />
                <span class="slider"></span>
            </label>
        </div>
    </section>

    <div class="scoring-sliders">
        <section class="report-scores">
            <h2>Proposal Score: {proposalScore[0]}</h2>
            <Slider bind:value={proposalScore} />

            <h2>Design Report Score: {designReportScore[0]}</h2>
            <Slider bind:value={designReportScore} />

            <h2>Participation Score: {participationScore[0]}</h2>
            <Slider
                max={participationScore[1]}
                bind:value={participationScore}
            />
        </section>

        <section class="mission-scores">
            <div class="mission-1">
                <h2>Mission 1: {m1Score[0]}</h2>
                <Slider max={m1Score[1]} bind:value={m1Score} />
            </div>

            <div class="mission-2">
                <h2>Mission 2: {calcM2Score().toFixed(2)}</h2>
                <h3>Laps: {m2Laps[0]}</h3>
                <Slider max={m2MaxLaps} bind:value={m2Laps} />
                <h3>
                    Passenger Count: {m2PassengerCnt[0]} (Required Area: {calcReqPassArea().toFixed(
                        2
                    )}
                    Sq. In.) (Weight: {calcPassWeight().toFixed(2)} oz.)
                </h3>
                <Slider
                    min={m2MinPassengerCnt}
                    max={m2MaxPassengerCnt}
                    bind:value={m2PassengerCnt}
                />
                <h3>
                    Cargo Count: {m2CargoCnt[0]} (Required Area: {calcReqCargoArea().toFixed(
                        2
                    )}
                    Sq. In.) (Weight: {calcCargoWeight().toFixed(2)} oz.)
                </h3>
                {#if calcMaxCargoCnt() === m2MinCargoCnt}
                    <div class="greyed-out">
                        <Slider max={m2MinCargoCnt} bind:value={m2CargoCnt} />
                    </div>
                {:else}
                    <Slider
                        min={m2MinCargoCnt}
                        max={calcMaxCargoCnt()}
                        bind:value={m2CargoCnt}
                    />
                {/if}
                <h3>Total Battery Capacity: {m2BatCap[0]} Watt-Hours</h3>
                <Slider
                    min={m2MinBatCap}
                    max={m2MaxBatCap}
                    bind:value={m2BatCap}
                />
            </div>

            <div class="mission-3">
                <h2>Mission 3: {calcM3Score().toFixed(2)}</h2>
                <h3>Laps: {m3Laps[0]}</h3>
                <Slider max={m3MaxLaps} bind:value={m3Laps} />
                <h3>Wingspan: {m3Wingspan[0]} Inches</h3>
                <Slider
                    min={m3MinWingspan}
                    max={m3MaxWingspan}
                    bind:value={m3Wingspan}
                />
                <h3>Banner Height: {m3BannerHeight[0]} Inches</h3>
                <Slider
                    min={m3BannerMinHeight}
                    max={m3BannerMaxHeight}
                    bind:value={m3BannerHeight}
                />
                <h3>Banner Length: {m3BannerLength[0]} Inches</h3>
                {#if calcMaxBannerLength() === m3BannerMinLength}
                    <div class="greyed-out">
                        <Slider
                            max={m3BannerMinLength}
                            bind:value={m3BannerLength}
                        />
                    </div>
                {:else}
                    <Slider
                        min={m3BannerMinLength}
                        max={calcMaxBannerLength()}
                        bind:value={m3BannerLength}
                    />
                {/if}
            </div>

            <div class="ground-mission">
                <h2>Ground Mission: {calcGMScore().toFixed(4)}</h2>
                <h3>Mission Time: {gmTime[0]} Seconds</h3>
                <Slider min={minGMTime} max={maxGMTime} bind:value={gmTime} />
            </div>
        </section>
    </div>
</div>

<!--
Custom slot: left: {range[0]}
right: {range[1]}
<div>
	<Slider max="200" step="10" bind:value={range} range on:input={e => console.log(e.detail)}/>
</div>
-->

<ThreeScene />

<style>
    .scoring {
        display: flex;
        flex-direction: column;
        gap: 1rem;
        padding: 1rem;
    }

    .calculated-scores {
        border: 2px solid var(--light-gray);
        border-radius: 0.5rem;
        padding: 0.5rem;
        box-shadow: 0 0 10px var(--white);
        margin-bottom: 1rem;
        color: var(--white);
    }

    section {
        border: 2px solid var(--light-gray);
        border-radius: 0.5rem;
        padding: 0.5rem;
        box-shadow: 0 0 10px var(--white);
        margin-bottom: 1rem;
        color: var(--white);
    }

    .scoring-sliders {
        --progress-bg: var(--scarlet);
        --track-bg: var(--off-white);
        --thumb-bg: var(--white);
    }

    .report-scores {
    }

    .mission-scores {
    }

    .mission-1 {
    }

    .mission-2 {
    }

    .mission-3 {
    }

    .ground-mission {
    }

    .greyed-out {
        --progress-bg: var(--black);
        pointer-events: none;
    }

    .toggle {
        position: relative;
        display: inline-block;
        width: 40px;
        height: 20px;
    }

    .toggle input {
        opacity: 0;
        width: 0;
        height: 0;
    }

    .slider {
        position: absolute;
        cursor: pointer;
        top: 0;
        left: 0;
        right: 0;
        bottom: 0;
        background-color: var(--light-gray);
        border: 2px solid var(--scarlet);
        border-radius: 34px;
        transition: 0.3s;
        box-shadow: 0 0 6px var(--scarlet);
    }

    .slider::before {
        position: absolute;
        content: "";
        height: 12px;
        width: 12px;
        left: 3px;
        bottom: 2px;
        background-color: var(--white);
        transition: 0.3s;
        border-radius: 50%;
    }

    .toggle input:checked + .slider {
        background-color: var(--scarlet);
        box-shadow: 0 0 6px var(--scarlet);
    }

    .toggle input:checked + .slider::before {
        transform: translateX(20px);
        background-color: var(--white);
    }
</style>
