<script>
    let height = 236.4;
    let angle = 50;
    let stepWoodThickness = 4;
    let stepWoodLength = 20.5;
    let vertWoodThickness = 2;
    let steps = 11;

    $: activeStep = Math.min(steps - 1, activeStep || 2);
    $: activeStep2 = activeStep + 1 > steps - 1 ? steps - 2 : activeStep + 1;

    $: calcHeightStep = height / steps;
    $: calcLengthStep =
        calcHeightStep * Math.tan(((90 - angle) / 180) * Math.PI);
    $: diagonalStep = Math.sqrt(
        Math.pow(calcHeightStep, 2) + Math.pow(calcLengthStep, 2)
    );
    $: totalHorz = calcLengthStep * steps;

    $: totalSlingerLength = Math.sqrt(
        Math.pow(totalHorz, 2) + Math.pow(height, 2)
    );

    $: K = calcHeightStep * Math.sin(((90 - angle) / 180) * Math.PI);
    $: L = Math.sqrt(Math.pow(calcHeightStep, 2) - Math.pow(K, 2));

    ////////

    $: trapregel = 2 * calcHeightStep + calcLengthStep;
    $: trapregelok = trapregel <= 63 && trapregel >= 57;

    $: blueprintWidth = calcLengthStep * steps;
    $: blueprintHeight = height;

    let lineWidth = 1;
    let fs = 13;
</script>

<main>
    <aside>
        <h1>Trapcalculator</h1>

        <fieldset>
            <legend>Parameters</legend>

            <label>
                <span><strong>A</strong> Hoogte</span>
                <input type="number" step="0.01" bind:value={height} /> cm
            </label>
            <label>
                <span><strong>B</strong> Aantal treden</span>
                <input type="number" bind:value={steps} />
            </label>
            <label>
                <span><strong>C</strong> Hoek</span>
                <input type="number" step="0.01" bind:value={angle} /> °
            </label>
            <label>
                <span><strong>D1</strong> Dikte hout optrede</span>
                <input
                    type="number"
                    step="0.01"
                    bind:value={stepWoodThickness}
                /> cm
            </label>
            <label>
                <span><strong>D2</strong> Lengte hout optrede (incl neus)</span>
                <input type="number" step="0.01" bind:value={stepWoodLength} /> cm
            </label>
            <label>
                <span><strong>E</strong> Dikte hout aantrede</span>
                <input
                    type="number"
                    step="0.01"
                    bind:value={vertWoodThickness}
                /> cm
            </label>
        </fieldset>
        <fieldset>
            <legend>Afgeleid</legend>

            <label title="Hoogte trede">
                <span><strong>F = A / B</strong></span>
                <input disabled value={calcHeightStep.toFixed(2)} /> cm
            </label>
            <label title="Lengte aantrede">
                <span><strong>G = F &times; tan(90 - C)</strong></span>
                <input disabled value={calcLengthStep.toFixed(2)} /> cm
            </label>
            <label title="Schuine doorsnee trede">
                <span
                    ><strong>H = √(F<sup>2</sup> + G<sup>2</sup>)</strong></span
                >
                <input disabled value={diagonalStep.toFixed(2)} /> cm
            </label>

            <label title="Horizontale overspanning">
                <span><strong>I = G &times; B</strong></span>
                <input disabled value={totalHorz.toFixed(2)} /> cm
            </label>

            <label title="Lengte trapboom">
                <span
                    ><strong>J = √(I<sup>2</sup> + A<sup>2</sup>)</strong></span
                >
                <input disabled value={totalSlingerLength.toFixed(2)} /> cm
            </label>

            <label title="Doorsnee ondertrede loodrecht op trapboom">
                <span><strong>K = F &times; sin(90 - C)</strong></span>
                <input disabled value={K.toFixed(2)} /> cm
            </label>

            <label title="Ondertrede op trapboom tot doorsnee K">
                <span
                    ><strong>L = √(F<sup>2</sup> - K<sup>2</sup>)</strong></span
                >
                <input disabled value={L.toFixed(2)} /> cm
            </label>

            <hr />

            <a
                href="https://nl.wikipedia.org/wiki/Trapformule"
                target="_blank"
                style={`color:black;text-decoration:none;width:max-content;background:${
                    trapregelok ? "#0F0" : "red"
                }`}
                >Trapformule {trapregelok
                    ? "binnen marges"
                    : "buiten marges"}</a
            >
            <label>
                <span
                    ><strong>2 optreden + 1 aantrede = 57 tot 63 cm</strong
                    ></span
                >
                <input disabled bind:value={trapregel} /> cm
            </label>

            <hr />

            Sjabloon trapboom

            <table>
                <thead>
                    <th>trede X</th>
                    <th align="right">L(X)</th>
                    <th align="right">H(X)</th>
                </thead>
                <tbody>
                    {#each Array(steps) as _, i}
                        <tr>
                            <td>{i + 1}</td>
                            <td align="right"
                                >{(diagonalStep * i + L).toFixed(2)} cm</td
                            >
                            <td align="right"
                                >{(diagonalStep * (i + 1)).toFixed(2)} cm</td
                            >
                        </tr>
                    {/each}
                </tbody>
            </table>
        </fieldset>
    </aside>

    <article style="position:relative">
        <fieldset style="position:absolute;top:10px;left:10px">
            <legend>Weergave</legend>

            <label>
                <span>Lijndikte</span>
                <input
                    type="range"
                    min="1"
                    max="5"
                    step="1"
                    bind:value={lineWidth}
                />
                &nbsp;{lineWidth}px
            </label>
            <label>
                <span>Fontgrootte</span>
                <input
                    type="range"
                    min="10"
                    max="20"
                    step="1"
                    bind:value={fs}
                />
                {fs}px
            </label>
        </fieldset>

        <div
            class="blueprint"
            style:width={blueprintWidth + "em"}
            style:height={blueprintHeight + "em"}
            style:--line={lineWidth + "px"}
            style:--fs={fs + "px"}
        >
            {#each Array(steps) as _, i}
                <!-- svelte-ignore a11y-no-static-element-interactions -->
                <div
                    class="step"
                    class:hovered-step={i == activeStep}
                    style:--angle={-angle + "deg"}
                    style:width={calcLengthStep + "em"}
                    style:height={calcHeightStep + "em"}
                    style:bottom={i * calcHeightStep + "em"}
                    style:left={i * calcLengthStep + "em"}
                    on:mouseenter={() => (activeStep = i)}
                >
                    {#if i == activeStep || i === activeStep2}
                        <div
                            class="step-labels"
                            class:has-offset={i === activeStep2}
                            style:top={(i === activeStep2
                                ? stepWoodThickness
                                : 0) + "em"}
                            style:left={(i === activeStep2
                                ? vertWoodThickness
                                : 0) + "em"}
                        >
                            <span
                                class="blueprint-label"
                                style="--top:50%;--left:-.8em">F</span
                            >
                            <span
                                class="blueprint-label"
                                style="--top:-.8em;--left:50%">G</span
                            >
                            <span
                                class="blueprint-label"
                                style="--top:calc(50% + .6em);--left:calc(50% + .6em)"
                                >H</span
                            >
                            <div
                                class="diagonal-step-line"
                                style:--angle={-angle + "deg"}
                                style:width={diagonalStep + "em"}
                            />
                            {#if i === activeStep2}
                                <span
                                    class="blueprint-label extra-line"
                                    style="--top:calc(25% + .3em);--left:calc(25% - .3em)"
                                    >K</span
                                >
                                <div
                                    class="diagonal-step-line extra-line"
                                    style="top:calc(-1 *var(--line) )"
                                    style:--angle={-angle + 90 + "deg"}
                                    style:width={K + "em"}
                                />

                                <span
                                    class="blueprint-label extra-line"
                                    style="--top:calc(75% + .55em);--left:calc(23% + .4em)"
                                    >L</span
                                >
                                <div
                                    class="diagonal-step-line extra-line"
                                    style:--angle={-angle + "deg"}
                                    style:width={L + "em"}
                                />
                            {/if}
                        </div>
                    {/if}

                    <div
                        class="step-horz"
                        style:width={stepWoodLength + "em"}
                        style:height={stepWoodThickness + "em"}
                    />
                    <div
                        class="step-vert"
                        style:top={stepWoodThickness + "em"}
                        style:width={vertWoodThickness + "em"}
                        style:height={calcHeightStep + "em"}
                    />
                </div>
            {/each}

            <div class="horz-length" style:width={totalHorz + "em"}>
                <span class="blueprint-label" style="--top:1.4em;--left:50%"
                    >I</span
                >
            </div>

            <div
                class="slinger"
                style:width={totalSlingerLength + "em"}
                style:left={vertWoodThickness + "em"}
                style:bottom={-stepWoodThickness + "em"}
                style:--angle={-angle + "deg"}
            >
                <span class="blueprint-label" style="--top:1.4em;--left:50%"
                    >J</span
                >
            </div>
        </div>
    </article>
</main>

<style>
    main {
        font-family: serif;
        top: 0;
        left: 0;
        position: fixed;
        height: 100%;
        width: 100%;
        display: flex;
        color: white;
        --ux-line: #ff0;
        flex-direction: column;
    }

    :global(*) {
        font-family: monospace;
    }
    h1 {
        margin-top: 0;
        font-size: 1.5rem;
    }
    aside {
        flex: 1;
        background: black;
        overflow: auto;
        padding: 10px 10px;
        box-sizing: border-box;
    }
    @media (min-width: 800px) {
        main {
            flex-direction: row;
        }
        aside {
            width: 40%;
            max-width: 400px;
        }
    }
    hr {
        border: none;
        height: 1px;
        background: var(--ux-line);
        width: 100%;
    }

    label {
        display: flex;
        gap: 10px;
        align-items: flex-start;
    }
    label span {
        flex: 1;
        max-width: 223px;
    }
    fieldset {
        display: grid;
        gap: 10px;
        border: 1px solid var(--ux-line);
    }
    fieldset + fieldset {
        margin-top: 15px;
    }
    legend {
        background: var(--ux-line);
        color: black;
        padding: 0 5px;
    }
    article {
        --bg: blue;
        overflow: auto;
        background: var(--bg);
        flex: 1;
        display: flex;
        align-items: center;
        justify-content: center;
        padding: 30px;
    }

    input {
        width: 4em;
    }

    .blueprint {
        position: relative;
        flex-shrink: 0;
        font-size: 0.33vmin;
        --linec: white;
        --worldc: #0f0;
        --stepc: black;
    }
    .blueprint::after {
        content: "";
        border-bottom: var(--line) solid var(--worldc);
        width: 100%;
        padding: 0 20px;
        left: -20px;
        bottom: 0;
        position: absolute;
    }
    .blueprint::before {
        content: "";
        border-top: var(--line) solid var(--worldc);
        border-left: var(--line) solid var(--worldc);
        border-bottom: var(--line) solid var(--worldc);
        width: 20em;
        height: 10em;
        top: 0;
        left: 100%;
        position: absolute;
    }

    .step {
        position: absolute;
    }
    .step::after {
        content: "";
        border-top: var(--line) solid var(--linec);
        border-left: var(--line) solid var(--linec);

        box-sizing: border-box;
        position: absolute;
        top: 0;
        left: 0;
        width: 100%;
        height: calc(100% + var(--line));
    }
    .hovered-step::after {
        background: linear-gradient(
            var(--angle),
            transparent 50%,
            rgba(255, 255, 255, 0.4) 50%
        );
    }

    .blueprint-label {
        position: absolute;
        top: var(--top);
        left: var(--left);
        width: 0;
        height: 0;
        display: flex;
        align-items: center;
        justify-content: center;
        color: var(--linec);
        font-size: var(--fs);
        font-weight: bold;
    }
    .step-horz {
        position: absolute;
        top: 0;
        right: 0;
        background: var(--stepc);
    }

    .step-vert {
        position: absolute;
        left: 0;
        background: var(--stepc);
    }

    .step-labels {
        position: absolute;
        width: 100%;
        height: 100%;
        left: 0;
        box-sizing: border-box;

        z-index: 10;

        --linec: #ff0;
        border-top: var(--line) solid var(--linec);
        border-left: var(--line) solid var(--linec);
    }

    .has-offset {
        --linec: #0ff;

        border-top: var(--line) solid var(--linec);
        border-left: var(--line) solid var(--linec);
    }
    .diagonal-step-line {
        z-index: 10;
        position: absolute;
        bottom: calc(-0.5 * var(--line));
        left: calc(-1 * var(--line));
        height: var(--line);
        transform: rotate(var(--angle));
        transform-origin: calc(0.5 * var(--line)) 0;
        background: var(--linec);
    }

    .horz-length {
        position: absolute;
        bottom: -7em;
        left: 0;
        height: 4em;
        box-sizing: border-box;
        z-index: 10;

        --linec: #ff0;
        border-right: var(--line) solid var(--linec);
        border-left: var(--line) solid var(--linec);
        border-bottom: var(--line) solid var(--linec);
    }

    .slinger {
        position: absolute;
        left: 0;
        box-sizing: border-box;
        z-index: 10;

        --linec: rgba(255, 150, 0, 0.7);
        border-bottom: var(--line) solid var(--linec);
        transform: rotate(var(--angle));
        transform-origin: 0 0;
    }

    .extra-line {
        --linec: #f0f;
    }

    input[type="range"] {
        height: 6px;
        -webkit-appearance: none;

        width: 60px;
        background: none;
    }
    input[type="range"]:focus {
        outline: none;
    }
    input[type="range"]::-webkit-slider-runnable-track {
        width: 60px;
        height: 6px;
        cursor: pointer;
        background: white;
        border-radius: 0;
        border: none;
    }
    input[type="range"]::-webkit-slider-thumb {
        height: 16px;
        width: 16px;
        background: #0ff;
        border: 1px solid black;
        box-sizing: border-box;
        cursor: pointer;
        -webkit-appearance: none;
        margin-top: -5px;
    }
</style>
