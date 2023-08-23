<script>
	import { onMount } from 'svelte';
    
    const clockBlocks = new Array(60);
    let active = [];
    let second = null;


    const numbers = [
        [1, 1, 1, 1, 1, 1, 0, 0, 0, 1, 1, 1, 1, 1, 1], // 0
        [1, 0, 0, 0, 1, 1, 1, 1, 1, 1, 0, 0, 0, 0, 1], // 1
        [1, 0, 1, 1, 1, 1, 0, 1, 0, 1, 1, 1, 1, 0, 1], // 2
        [1, 0, 1, 0, 1, 1, 0, 1, 0, 1, 1, 1, 1, 1, 1], // 3
        [1, 1, 1, 0, 0, 0, 0, 1, 0, 0, 1, 1, 1, 1, 1], // 4
        [1, 1, 1, 0, 1, 1, 0, 1, 0, 1, 1, 0, 1, 1, 1], // 5
        [1, 1, 1, 1, 1, 1, 0, 1, 0, 1, 1, 0, 1, 1, 1], // 6
        [1, 0, 0, 0, 0, 1, 0, 1, 1, 1, 1, 1, 0, 0, 0], // 7
        [1, 1, 1, 1, 1, 1, 0, 1, 0, 1, 1, 1, 1, 1, 1], // 8
        [1, 1, 1, 0, 1, 1, 0, 1, 0, 1, 1, 1, 1, 1, 1]  // 9
    ];

    const updatePerSecond = true;

	onMount( () => {
		const interval = setInterval(() => {
			
		}, updatePerSecond ? 1000 : 60000);


        /**
	 * @type {any[]}
	 */
    const blocks = [];
    const digits = Array.from(document.querySelectorAll('.block'));

    for (let i = 0; i < 4; i++) {
        blocks.push(digits.slice( i * 15, i * 15 + 15 ));
    }

    const setNum = (block, num) => {
        let n = numbers[num];
        for (let i = 0; i < block.length; i++) {
            block[i].classList[ n[i] === 1 ?  'add' : 'remove']('active');
        }
    };

    const time = {
        s: '',
        m: '',
        h: '',
        p: null
    };

        // time loop
        const animator = () => {
            let d = new Date(),
                h = d.getHours().toString(),
                m = d.getMinutes().toString(),
                s = d.getSeconds().toString();
            
            s = s.length === 1 ? '0' + s : s;
            m = m.length === 1 ? '0' + m : m;
            h = h.length === 1 ? '0' + h : h;
            
            if (updatePerSecond && s !== time.s) {
                for (let i = 0; i < digits.length; i++) {
                    let d = digits[i];

                    if (i === +s) {
                        second = i
                        if (time.p !== null)
                            digits[time.p].classList.remove('second');
                        time.p = i;
                        time.s = s;
                    }
                }
            }
            
            if (m !== time.m) {
                setNum(blocks[2], m[0]);
                setNum(blocks[3], m[1]);
                time.m = m;
            }
            
            if (h !== time.h) {
                setNum(blocks[0], h[0]);
                setNum(blocks[1], h[1]);
                time.h = h;
            }
            window.requestAnimationFrame(animator)
        }

        // init
        window.requestAnimationFrame(animator)


	});

</script>

<style>
    .clock {
        position: relative;
        display: flex;
        flex-flow: column wrap;
        width: 700px;
        height: 350px;
    }

    .block {
        width: calc(100% / 12);
        height: 20%;
        color: #474747;
        transition: 0.4s;
    }
    .block:not(:nth-child(n+16)):nth-child(n+11), .block:not(:nth-child(n+46)):nth-child(n+41) {
        margin-right: 30px;
    }
    .block:not(:nth-child(n+31)):nth-child(n+26) {
        margin-right: 60px;
    }
    .block:before {
        content: attr(data-num);
        position: relative;
        display: block;
        font-size: 45px;
        width: 100%;
        height: 100%;
        line-height: 70px;
        text-align: center;
    }
    .block:nth-child(-n+10):before {
        content: "0" attr(data-num);
    }
    .block.active:before {
        color: whitesmoke;
        font-weight: 500;
    }
    .block.second:before {
        color: #FF8300;
    }

    .divider {
        position: absolute;
        width: 2px;
        background-color: whitesmoke;
        height: 60%;
        top: 20%;
        left: calc(50% + 55px);
    }

</style>



<div class="clock">
    {#each clockBlocks as _block, i}
        <div class="block" data-num={i} class:active={active.includes(i)} class:second={second === i}/>
	{/each}
    
    
    <div class="divider"></div>
</div>