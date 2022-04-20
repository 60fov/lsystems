<script lang='ts'>
import { afterUpdate } from "svelte";



    type State = {
        x: number
        y: number
        a: number
    }
    export let width = window.innerWidth;
    export let height = window.innerHeight;

    export let input = "";
    export let x0 = width/2;
    export let y0 = height/2;
    export let a0 = 0;

    export let d = 10;
    export let b = Math.PI;

    
    let pathD = "";
    let svg: SVGGraphicsElement;
    let path: SVGPathElement;
    let box: DOMRect;
    let tx: number = 0;
    let ty: number = 0;

    // $: {
    //     pathD;
    //     setTimeout(() => {
    //         box = path.getBoundingClientRect();
            
    //         tx = (width - box.width) / 2;
    //         ty = (height - box.height) / 2;
    //     }, 0)
    // }

    afterUpdate(() => {
        // console.log('fff')
        // let box = path?.getBBox();
        // tx = (width - box.width) / 2 - box.x;
        // ty = (height) / 4 * 3;
        // console.log(tx,ty);
        // console.log(path?.getBBox());
        // console.log("\n\n\n");
        
    })

    

    $: {
        let stack: State[] = [];
        let x = x0;
        let y = y0;
        let a = a0;
        pathD = `M${x0} ${y0}`;
        input.split('').forEach((char: string) => {
            let dist = d;
            switch(char) {
                case 'F':
                    x += dist * Math.cos(a);
                    y += dist * Math.sin(a);
                    pathD += `L${Math.round(x)} ${Math.round(y)}`;
                break;
                case 'f':
                    x += dist * Math.cos(a);
                    y += dist * Math.sin(a);
                    pathD += `M${Math.round(x)} ${Math.round(y)}`;
                break;
                case '+':
                    a += b;
                break;
                case '-':
                    a -= b;
                break;
                case ']':
                    if (stack.length > 0) ({x, y, a} = stack.pop());
                    pathD += `M${Math.round(x)} ${Math.round(y)}`;
                break;
                case '[':
                    stack.push({x, y, a});
                break;
            }
        });
    }

    function svgScroll(event: WheelEvent) {
        let dx = event.deltaX;
        let dy = event.deltaY;
        tx += dx;
        ty += dy;
    }


</script>

<svg bind:this={svg} on:wheel={svgScroll} {width} {height}>
    <path bind:this={path} d={pathD} transform={`translate(${tx}, ${ty})`} fill=transparent stroke=black/>
</svg>

<style lang='scss'>
    svg {
        position: absolute;
        left: 0;
        right: 0;
        width: 100%;
        height: 100%;
    }

</style>