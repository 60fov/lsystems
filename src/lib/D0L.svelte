<script lang='ts'>
    import SvgTurtle from "./SvgTurtle.svelte";

    type Lsystem = {
        vars: string[]
        axiom: string
        preds: string[]
        succs: string[]
    }

    type LsystemSettings = {
        str: string
        angle: number
        n: number
        d: number
    }

    const rad = (deg: number) => Math.PI / 180 * deg;

    const presets = [
        {
            name: "tree 1",
            str: "F,F,F>F[+F]F[-F]F",
            n:5,
            d:3,
            angle:25.7
        },
        {
            name: "tree 2",
            str: "F,F,F>F[+F]F[-F][F]",
            n:5,
            d:10,
            angle:20
        },
        {
            name: "tree 3",
            str: "F,F,F>FF-[-F+F+F]+[+F-F-F]",
            n:4,
            d:12,
            angle:22.5
        },
        {
            name: "tree 4",
            str: "FX,X,X>F[+X]F[-X]+X F>FF",
            n:7,
            d:3,
            angle:20
        },
        {
            name: "tree 5",
            str: "FX,X,X>F[+X][-X]FX F>FF",
            n:7,
            d:3,
            angle:25.7
        },
        {
            name: "tree 6",
            str: "FX,X,X>F-[[X]+X]+F[+FX]-X F>FF",
            n:5,
            d:7,
            angle:22.5
        },
        {
            name: "Koch island",
            str: "F,F-F-F-F,F>F-F+F+FF-F-F+F",
            n: 3,
            d: 7,
            angle:90
        },
        {
            name: "Koch island and lakes",
            str: "Ff,F+F+F+F,F>F+f-FF+F+FF+Ff+FF-f+FF-F-FF-Ff-FFF f>ffffff",
            n: 2,
            d: 7,
            angle:90
        },
        {
            name: "dragon curve",
            str: "LR,L,L>L+R R>L-R",
            n: 10,
            d: 7,
            angle:90
        },
        {
            name: "Sierpinski gasket",
            str: "LR,R,L>R+L+R R>L-R-L",
            n: 6,
            d: 7,
            angle:60
        },
        {
            name: "hexagonal Gosper curve",
            str: "LR,L,L>L+R++R-L--LL-R+ R>-L+RR++R+L--L-R",
            n: 4,
            d: 7,
            angle:60
        },
    ]
    
    let currentPreset = 0;
    $: settings = presets[currentPreset];


    $: lsystem = parseLsystem(settings.str);
    $: model = modelLsystem(lsystem, settings.n);

    function parseLsystem(lsysStr: string) {
        if (!lsysStr) return;
        let secs = lsysStr.split(',');
        if (secs.length < 3) {
            console.error("invalid l-system");
            return {} as Lsystem;
        }
        let vars = secs[0].split('');
        let axiom = secs[1];
        let rules = secs[2].split(' ');
        let preds = new Array<string>(rules.length);
        let succs = new Array<string>(rules.length);
        
        for(let i = 0; i < rules.length; i++) {
            const rule = rules[i];
            let pred_succ = rule.split('>');
            if (pred_succ.length != 2) {
                console.error(`invalid rule: ${rule}`);
                return {} as Lsystem;
            }
            preds[i] = pred_succ[0];
            succs[i] = pred_succ[1];
        }

        let result = {vars, axiom, preds, succs} as Lsystem;
        console.log("valid l-system");
        return result;
    }

    function modelLsystem(sys: Lsystem, steps: number) {
        if (!sys?.axiom) return;
        let result = sys.axiom;
        for(; steps > 0; steps--) {
            result = result.split('').map((c) => {
                let i = sys.preds.indexOf(c);
                return i < 0 ? c : sys.succs[i];
            }).join('');
        }
        return result;
    }

    function modelToTurtle(sys: Lsystem, model: string) {
        if (!sys?.vars) return;
        sys.vars.forEach((v: string) => model = model.replaceAll(/[A-Z]/g, 'F').replaceAll(/[a-z]/g, 'f'));
        return model;

    }

</script>

<div class="wrapper">
    <input bind:value={settings.str} type=text placeholder=L-System>
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
    </div>
    <SvgTurtle 
    input={modelToTurtle(lsystem, model) || ""} 
    a0={-Math.PI / 2} 
    d={settings.d} 
    b={rad(settings.angle)}></SvgTurtle>
</div>

<style lang='scss'>

    .wrapper {
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

</style>