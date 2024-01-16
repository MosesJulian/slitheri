<script lang="ts">
	import { onMount } from "svelte";

  type Coordinate = {
    x: number;
    y: number;
  };

  const getRandomFood = () => {
    return {
      x: Math.floor(Math.random() * 20) * 30,
      y: Math.floor(Math.random() * 20) * 30,
    }
  }

  const clearCanvas = (ctx: CanvasRenderingContext2D) => {
    for (let row = 0; row <= 20; row++) {
      for (let column = 0; column <= 20; column++) {
        if ((row + column) % 2 === 0) {
          ctx.fillStyle = '#0D2818';
        } else {
          ctx.fillStyle = '#058C42';
        }
        ctx.fillRect(row * 30, column * 30, 30, 30);
      }
    }
  }

  const drawSnake = (ctx: CanvasRenderingContext2D) => {
    ctx.fillStyle = '#17BEBB';
    snake.forEach((snakePart) => {
      ctx.fillRect(snakePart.x, snakePart.y, 30, 30);
    });
  }

  const drawFood = (ctx: CanvasRenderingContext2D) => {
    ctx.fillStyle = '#D62246';
    if (snake.some((snakePart) => snakePart.x === foodPosition.x && snakePart.y === foodPosition.y)) foodPosition = getRandomFood();
    ctx.fillRect(foodPosition.x, foodPosition.y, 30, 30);
  }

  const ateFood = () => {
    return snake[0].x === foodPosition.x && snake[0].y === foodPosition.y;
  }

  const isColliding = () => {
    const isCollidingWithHorizontalWalls = snake[0].x < 0 || snake[0].x > 570;
    const isCollidingWithVerticalWalls = snake[0].y < 0 || snake[0].y > 570;
    const isCollidingWithBody = snake.slice(1).some((snakePart) => snakePart.x === snake[0].x && snakePart.y === snake[0].y);

    return isCollidingWithHorizontalWalls || isCollidingWithVerticalWalls || isCollidingWithBody;
  }

  const moveSnake = (deltaTime: number) => {
    if (timeSincePrevMove >= 150) {
      timeSincePrevMove = 0;
    } else {
      timeSincePrevMove += deltaTime;
      return;
    }

    const head = {x: snake[0].x + velX, y: snake[0].y + velY};
    snake.unshift(head);
    
    if (ateFood()) {
      foodPosition = getRandomFood();
      score++;
    } else {
      snake.pop();
    }
  }

  let canvas: HTMLCanvasElement | null;

  let snake: Array<Coordinate>;

  let timeSincePrevMove: number;
  let velX: number, velY: number;

  let foodPosition: Coordinate;

  let score: number;

  let isDialogOpen: boolean;

onMount(() => {
  const ctx = canvas?.getContext('2d');
  let prevTime = Date.now();

  const gameLoop = () => {
    const now = Date.now();
    const deltaTime = now - prevTime;

    prevTime = now;

    clearCanvas(ctx!);
    drawSnake(ctx!);
    drawFood(ctx!);
    moveSnake(deltaTime);
    ateFood();

    if (isColliding()) {
      // show dialog/modal
      isDialogOpen = true;

      return;
    };

    requestAnimationFrame(gameLoop);
  }

  const startGame = () => {
    snake = [{x: 30, y: 30}];
    foodPosition = getRandomFood();

    velX = 30;
    velY = 0;

    timeSincePrevMove = 0;

    score = 0;

    isDialogOpen = false;

    requestAnimationFrame(gameLoop);
  };

  startGame();

  document.addEventListener('keydown', (event: KeyboardEvent) => {
    if (event.key === "ArrowUp" && velY !== 30) {
      velX = 0;
      velY = -30;
      setInterval(() => {}, 150);
    }
    if (event.key === "ArrowDown" && velY !== -30) {
      velX = 0;
      velY = 30;
      setInterval(() => {}, 150);
    }
    if (event.key === "ArrowLeft" && velX !== 30) {
      velX = -30;
      velY = 0;
      setInterval(() => {}, 150);
    }
    if (event.key === "ArrowRight" && velX !== -30) {
      velX = 30;
      velY = 0;
      setInterval(() => {}, 150);
    }
  })
});
</script>

<main class="min-h-screen grid place-items-center bg-slate-100">
  <div class="flex flex-col items-center gap-10 max-w-3xl w-full">
    <h1 class="text-6xl text-center font-semibold">slitheri</h1>

    <h3 class="text-3xl text-left">Score: {score}</h3>
    
    <canvas bind:this={canvas} width="600" height="600"></canvas>

    {#if isDialogOpen}
      <div class="absolute top-0 bottom-0 left-0 right-0 z-10 grid place-items-center bg-[rgba(0,0,0,0.5)]">
        <div class="shadow bg-white p-4 rounded flex flex-col gap-2 justify-center">
          <div class="text-xl flex justify-between items-center px-4">
            <h2>Game Over</h2>
          </div>
          <hr />
          <h4 class="text-lg">Your Score is: {score}</h4>
          <button class="text-white px-4 py-2 rounded bg-slate-500 duration-300 transition-colors hover:bg-slate-700" on:click={() => location.reload()}>Play Again</button>
        </div>
      </div>
    {/if}
  </div>
</main>