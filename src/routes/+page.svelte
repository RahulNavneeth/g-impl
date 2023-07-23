<script lang="ts">
    import { onMount } from "svelte";
    import { writable } from "svelte/store";
    import { color } from "../lib/index";

    let gridStore = writable<Array<Array<number>>>([]);

    onMount(() => {
        const GRID_WIDTH = Math.ceil(window.innerWidth / 75);
        const GRID_HEIGHT = Math.ceil(window.innerHeight / 75);

        let grid: Array<Array<number>> = [...Array(GRID_HEIGHT)].map((_, x) =>
            [...Array(GRID_WIDTH)].map((_, y) => {
                if (
                    x === 0 ||
                    x === GRID_HEIGHT - 1 ||
                    y === 0 ||
                    y === GRID_WIDTH - 1
                ) {
                    return -2;
                } else {
                    return -1;
                }
            })
        );

        gridStore.set(grid);
    });

    const bfs = (r: number, c: number) => {
        const queue: Array<[[number, number], number]> = [];
        queue.push([[r, c], 0]);

        const grid = $gridStore;
        let count = 0;

        const processNextNode = () => {
            if (queue.length === 0) return;

            const [[x, y], w] = queue.shift() || [[0, 0], 0];
            if (grid[x][y] === -2) {
                processNextNode();
                return;
            }

            let dir = [
                [0, 1],
                [1, 0],
                [0, -1],
                [-1, 0],
            ];

            for (let i = 0; i < 4; i++) {
                let nx: number = x + dir[i][0];
                let ny: number = y + dir[i][1];
                if (grid[nx][ny] === -1) {
                    let nl = w + 1;
                    count = Math.max(count, nl);
                    grid[nx][ny] = nl;
                    queue.push([[nx, ny], nl]);
                }
            }
            gridStore.set(grid);
            setTimeout(processNextNode, 100);
        };

        processNextNode();
    };
</script>

<div>
    {#each $gridStore as row, id_x}
        <div class="flex">
            {#each row as cell, id_y}
                <button
                    style="background-color: {cell === -2
                        ? '#e1bee7'
                        : color[cell]};"
                    class="w-[75px] h-[75px] border-[1px] border-black hover:bg-[#e1bee7] transition-all"
                    on:click={() => {
                        bfs(id_x, id_y);
                    }}
                />
            {/each}
        </div>
    {/each}
</div>
