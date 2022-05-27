<script lang="ts">
    import NumberInput from "./NumberInput.svelte";
    import TextInput from "./TextInput.svelte";
    import SvgTurtle from "./SvgTurtle.svelte";

    import { presets } from "./misc";

    type Lsystem = {
        vars: string[];
        axiom: string;
        preds: string[];
        succs: string[];
    };

    type LsystemSettings = {
        str: string;
        angle: number;
        n: number;
        d: number;
    };

    const rad = (deg: number) => (Math.PI / 180) * deg;

    let currentPreset = 0;
    $: settings = presets[currentPreset];

    let lsystem: Lsystem;
    let model: string;

    $: {
        lsystem = parseLsystem(settings.str);
        model = modelLsystem(lsystem, settings.n);
    }

    function parseLsystem(lsysStr: string) {
        if (!lsysStr) return;
        let secs = lsysStr.split(",");
        if (secs.length < 3) {
            console.error("invalid l-system");
            return {} as Lsystem;
        }
        let vars = secs[0].split("");
        let axiom = secs[1];
        let rules = secs[2].split(" ");
        let preds = new Array<string>(rules.length);
        let succs = new Array<string>(rules.length);

        for (let i = 0; i < rules.length; i++) {
            const rule = rules[i];
            let pred_succ = rule.split(">");
            if (pred_succ.length != 2) {
                console.error(`invalid rule: ${rule}`);
                return {} as Lsystem;
            }
            preds[i] = pred_succ[0];
            succs[i] = pred_succ[1];
        }

        let result = { vars, axiom, preds, succs } as Lsystem;
        console.log("valid l-system");
        return result;
    }

    function modelLsystem(sys: Lsystem, steps: number) {
        if (!sys?.axiom) return;
        let result = sys.axiom;
        for (; steps > 0; steps--) {
            result = result
                .split("")
                .map((c) => {
                    let i = sys.preds.indexOf(c);
                    return i < 0 ? c : sys.succs[i];
                })
                .join("");
        }
        return result;
    }

    function modelToTurtle(sys: Lsystem, model: string) {
        if (!sys?.vars) return;
        sys.vars.forEach(
            (v: string) =>
                (model = model
                    .replaceAll(/[A-Z]/g, "F")
                    .replaceAll(/[a-z]/g, "f"))
        );
        return model;
    }
</script>

<!-- <input bind:value={settings.str} type=text placeholder=L-System>
    <div style:display="flex">
        <label for=angle style:margin-right=10px>angle</label>
        <input id=angle bind:value={settings.angle} type=range min=0 max=360>
        <input style:width=6ch bind:value={settings.angle} type=number min=0 max=360 placeholder=angle>
    </div>
    <div style:display="flex">
        <label style:margin-right="10px" for=n>n</label>
        <input id=n bind:value={settings.n} type=number min=0>
    </div>
    <div style:display="flex">
        <label style:margin-right="10px" for=d>d</label>
        <input id=d bind:value={settings.d} type=range min=1>
    </div>
    <div>
        <label for="preset">presets</label>
        <select id=preset bind:value={currentPreset}>
            {#each presets as preset, i}
                <option value={i}>{preset.name}</option>
            {/each}
        </select>
    </div> -->
<div class="input-wrapper">
    <!-- <List></List> -->
    <TextInput label="rules"  bind:value={settings.str} />
    <TextInput label="axiom" bind:value={lsystem.axiom} />
    <div class="input-group">
        <NumberInput label="a" max={360} bind:value={settings.angle} threshold={1} />
        <NumberInput label="n" max={10} bind:value={settings.n} />
        <NumberInput label="d" min={1} max={30} bind:value={settings.d} />
    </div>
    <div class="select-wrapper">
        <label for="preset">presets</label>
        <select id="preset" bind:value={currentPreset}>
            {#each presets as preset, i}
                <option value={i}>{preset.name}</option>
            {/each}
        </select>
    </div>
</div>

<div class="turtle-wrapper">
    <SvgTurtle
        input={modelToTurtle(lsystem, model) || ""}
        a0={-Math.PI / 2}
        d={settings.d}
        b={rad(settings.angle)}
    />
</div>

<style lang="scss">
    .turtle-wrapper {
        display: flex;
        flex-direction: column;
        align-items: flex-start;
        justify-content: flex-end;
        width: 80%;
        margin: auto;
        height: 100vh;

        * {
            margin-bottom: 5px;
            z-index: 1;
        }
    }

    .input-wrapper {
        z-index: 10;
        width: 40%;
        max-width: 400px;
        min-width: 250px;
        position: absolute;
        bottom: 25px;
        left: 25px;
        padding: 10px;
        display: flex;
        flex-direction: column;
        justify-content: stretch;
        align-items: center;
        gap: 5px;
    }

    .input-group {
        display: flex;
        justify-content: stretch;
        align-items: center;
        gap: 5px;
    }

    .select-wrapper {
        width: 100%;

        display: flex;
        align-items: center;

        border: var(--border);

        select,
        label {
            padding: var(--padding);
        }

        label {
            width: fit-content;
            background: rgb(224, 224, 224);
            border-right: var(--border);
            text-transform: uppercase;
        }

        select {
            width: 100%;
            background: none;
            border: none;
            margin-right: 10px;
            text-transform: uppercase;
        }
    }
</style>
