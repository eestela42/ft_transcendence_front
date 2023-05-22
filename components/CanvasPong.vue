<template>
    <div class="game-container">
  
    <div class="info-container">
      <p>{{ "veloBall x-y " + retVeloBallX + " " + retVeloBallY }}</p>
      <p ></p>
      <p>{{ "Pong " + pong }}</p>
    </div>
  
    <div class="button-container">
      <button @click="startMatchSolo" :disabled="gameIsRunning">Solo</button>
      <button @click="startMatchMultiLocal" :disabled="gameIsRunning">MultiplayerLocal</button>
      <button @click="startNoPlayer" :disabled="gameIsRunning">NoPlayer</button>
      <button @click="restartMatch" :disabled="!gameIsRunning">Restart</button>
      <button @click="setBlocks" :disabled="gameIsRunning">{{"BLOCKS "+ blockStatus}}</button>

    </div>
    <div class="scores">
      <div class="scorePlayer" left="100%">{{ "Score A : " + scoreA }}</div>
      <div class="scorePlayer" right="100%">{{ "Score B : " + scoreB }}</div>
    </div>
  </div>
  <div class="pong-container" >
    <canvas ref = "myCanvas" class="gameCanvasStyle" ></canvas>
</div>

  </template>
  
  <style>
  
    .pong-container  {
        z-index: 0;
        display: flex;
        position: relative;
        margin-right: 25%;
        overflow: visible;
        width: 100%;
        height: 100%;
        background-color: #bbebfa;
        border: solid rgb(82, 79, 79);

    }
    
 
    
    .flex-item {
        flex: 1;
    }

    .gameCanvasStyle {
    z-index: 1;
    position: relative;
    display: flex;
    overflow: visible;
    width: 100%;
    height: 100%;
    border: solid rgb(156, 0, 0);
    background-color: #708a75;

    margin: auto; 
    
  }
  
  .info-container {
    position: absolute;
    top: 0;
    bottom: 5%;
    left: 1%;
    margin-top: 50px;
    height: 100px;
    width: 10%;
  }
  
  .button-container {
    position: absolute;
    bottom: 100%;  /* Make the button container stick to the top of the game container */
    left: 0;       /* Align to the left of the game container */
    height: auto;  /* Let the height be determined by its content */
    width: 100%;   /* Full width to match game container */
    display: flex; /* Make it a flex container */
    justify-content: center; /* Center buttons horizontally */
    align-items: center; /* Center buttons vertically */
    gap: 10px; /* Add some space between the buttons */
  }
  
  .game-container {
    flex: 1;
    position: relative;
    display: flex;
    flex-direction: column;

    margin-top: 5%;
    right: 1%;
    padding-top: 120px; /* Space for the buttons. This should be greater than the height of the buttons */
  }
  

  
  .input-container {
  display: flex;
  justify-content: space-between;
  align-items: center;
  
  }

  
  .scores {
  display: relative;
  height: 40px;
  font-size: 24px;
  color: #3b133b;
  }
  
  .scorePlayer {
  flex: 1;
  }
  
  
  </style>
  
  <script lang="ts">
  
  import { defineComponent,onMounted, onUnmounted,ref, computed } from 'vue';
  export default defineComponent({
setup() {

const   myCanvas = ref<(HTMLCanvasElement | null)>(null);
let     ctx :CanvasRenderingContext2D | null = null;


const pongWidth = 900;
const pongHeight = 600;

// CONTROL PARAMETERS
let rightPlayerKeyUp = 'ArrowUp';
let rightPlayerKeyDown = 'ArrowDown';
let leftPlayerKeyUp = 'w';
let leftPlayerKeyDown = 's';


// BALL PARAMETERS
let ballX = pongWidth / 2;
let ballY = pongHeight / 2;

let veloBallX = 0;
let veloBallY = 0;

let ballSize = 10;
let paddleOffset = 20;

let gameTick = 16;
let ballMaxSpeedX = 35;
let ballMaxSpeedY = 10;
let bounce = 25;

// PADDLE PARAMETERS

const paddleWidth = 10;
const paddleHeight = 80;
const paddleSpeed = 12;

// LEFT PADDLE PARAMETERS
let leftPaddleWidth = paddleWidth;
let leftPaddleHeight = paddleHeight;

let leftPaddleY = pongHeight / 2 - leftPaddleHeight / 2;
let leftPaddleSpeed = paddleSpeed;

// RIGHT PADDLE PARAMETERS
let rightPaddleWidth = paddleWidth;
let rightPaddleHeight = paddleHeight;

let rightPaddleY = pongHeight / 2 - rightPaddleHeight / 2;
let rightPaddleSpeed = paddleSpeed;

//BLOCK PARAMETERS

let blockWidth = pongHeight * 0.05;
let blockHeight = pongWidth * 0.05;

let whatId = 0;

//GAME VARIABLES
let gameIsRunning = ref(false);
let gameIsBlocks = false;
const blockStatus = ref('DISABLED');

let scoreA = ref(0);
let scoreB = ref(0);


let ping = ref(0);
let pong = ref(0);

const retVeloBallX = ref(0);
const retVeloBallY = ref(0);

let alreadyComputed = false;

let leftArrowUp = 0;
let leftArrowDown = 0;

let rightArrowUp = 0;
let rightArrowDown = 0;

let x = 1;
let line = '';

let myBlocks:(SolidBlock | EffectBlock)[] = [];



const startMatchSolo = () => {
  if (!gameIsRunning.value)
  {
    const rand = Math.random() * 2 - 1;
    veloBallX = rand + (rand / Math.abs(rand)) * 2;
    veloBallY = Math.random() * 6 - 3;

    ballX = pongWidth/2 - ballSize/2;
    ballY = pongHeight/2 - ballSize/2;

    rightPaddleHeight = pongHeight;
    rightPaddleY = 0;

    rightPlayerKeyDown = '';
    rightPlayerKeyUp = '';

    gameIsRunning.value = true;
  }
}


const startNoPlayer = () => {
  if (!gameIsRunning.value)
  {
    const rand = Math.random() * 2 - 1;
    veloBallX = rand + (rand / Math.abs(rand)) * 2;
    veloBallY = Math.random() * 6 - 3;

    ballX = pongWidth/2 - ballSize/2;
    ballY = pongHeight/2 - ballSize/2;

    rightPaddleHeight = pongHeight;
    rightPaddleY = 0;

    rightPlayerKeyDown = '';
    rightPlayerKeyUp = '';

    leftPaddleHeight = pongHeight;
    leftPaddleY = 0;

    leftPlayerKeyDown = '';
    leftPlayerKeyUp = '';

    gameIsRunning.value = true;
  }
  }

const startMatchMultiLocal = () => {
  if (!gameIsRunning.value)
  {
    const rand = Math.random() * 2 - 1;
    veloBallX = rand + (rand / Math.abs(rand)) * 2;
    veloBallY = Math.random() * 6 - 3;

    ballX = pongWidth/2 - ballSize/2;
    ballY = pongHeight/2 - ballSize/2;

    gameIsRunning.value = true;
  }
}

const restartMatch = () => {
  if (gameIsRunning.value) {
    scoreA.value = 0;
    scoreB.value = 0;

    veloBallX = 0;
    veloBallY = 0;

    rightPaddleHeight = 80;
    rightPaddleY = pongHeight/2 - rightPaddleHeight/2;

    rightPlayerKeyDown = 'ArrowDown';
    rightPlayerKeyUp = 'ArrowUp';

    leftPaddleHeight = 80;
    leftPaddleY = pongHeight/2 - leftPaddleHeight/2;

    leftPlayerKeyDown = 's';
    leftPlayerKeyUp = 'w';

    ballX = pongWidth / 2 - ballSize / 2;
    ballY = pongHeight / 2 - ballSize / 2;

    ping.value = 0;
    pong.value = 0;

    gameIsRunning.value = false;

    myBlocks = [];
  }
}

const setBlocks = () => {
  if (!gameIsBlocks)
  {
    gameIsBlocks = true;
    blockStatus.value = 'ENABLED';
  }
  else
  {
    gameIsBlocks = false;
    blockStatus.value = 'DISABLED';
  }
}

/***********************BALL COLISIONS***********************/

const ballWallColision = () => {
  if (ballY <= 0 )
    veloBallY = Math.abs(veloBallY);
  else if (ballY >= pongHeight - ballSize)
  veloBallY = -Math.abs(veloBallY);

  if (ballX <= 1)
    {
      scoreB.value += 1;
      ballX = pongWidth/2 - ballSize/2;
      ballY = pongHeight/2 - ballSize/2;
      veloBallX = (2 + Math.random());
      veloBallY = (Math.random() * 6) - 3;
    }
  if (ballX >= pongWidth - ballSize - 1)
    {
      scoreA.value += 1;
      ballX = pongWidth/2 - ballSize/2;
      ballY = pongHeight/2 - ballSize/2;
      veloBallX = -(2 + Math.random());
      veloBallY = (Math.random() * 6) - 3;
    }
}

const ballPaddleColision =  (paddleX: number, paddleY: number, paddleHeight:number, sign: number):boolean => {
if (sign * ballX <= paddleX)
{
  if (ballY >= paddleY && ballY <= paddleY + paddleHeight)
  {
    veloBallX = sign * (Math.abs(veloBallX) + ((ballY - paddleY) / (paddleHeight / 2)));
    if (veloBallX >= ballMaxSpeedX || veloBallX <= -ballMaxSpeedX)
      veloBallX = ballMaxSpeedX * sign;
    veloBallY = ( (ballY - (paddleY + paddleHeight/2)) / paddleHeight/2)  * bounce + Math.random();
    if (gameIsBlocks && (ping.value + pong.value) % 10 < 5)
      generateBlocks();
    return true;
  }
}
return false;
}

const ballBlockColision = () =>
{
  for (let block of myBlocks) {
    if (ballX <= block.x + block.width && ballX + ballSize >= block.x)
    {
      if (ballY <= block.y + block.height && ballY + ballSize >= block.y)
      {
        if (block instanceof SolidBlock && block.isSolid)
          veloBallX = -veloBallX;
        else if (block instanceof EffectBlock)
          block.triggerEffect();
        removeBlock(block.id);
      }
    }
}
}

const preColision = ():boolean => {
  const oldVeloX = veloBallX;
  const oldVeloY = veloBallY;

  if (ballPaddleColision(paddleOffset + leftPaddleWidth, leftPaddleY,leftPaddleHeight, 1)
    || ballPaddleColision( (paddleOffset + rightPaddleWidth + ballSize) - pongWidth, rightPaddleY, rightPaddleHeight, -1))
  {
    if (ballX < 200)
    {
      ping.value += 1;
      ballX = paddleOffset + leftPaddleWidth + ballSize/2 + 1;
    }
    else
    {
        pong.value += 1;
      ballX = pongWidth - (paddleOffset + ballSize + rightPaddleWidth + 1);
    }
    return true;
  }
    veloBallX = oldVeloX;
    veloBallY = oldVeloY;
    return false;
}

const ballColision = () =>
{
    console.log('\nball x = ' + ballX + '\nball y = ' + ballY + '\nvelo x = ' + veloBallX + '\nvelo y = ' + veloBallY + '\n-------\n');
  if (alreadyComputed)
  {
    alreadyComputed = false;
    ballX += veloBallX;
    ballY += veloBallY;

    return;
  }
  ballWallColision();
  ballBlockColision();
  const nbr = ping.value + pong.value;
  if (ballPaddleColision(paddleOffset + leftPaddleWidth, leftPaddleY,leftPaddleHeight, 1))
    ping.value += 1;
  else if (ballPaddleColision( (paddleOffset + rightPaddleWidth + ballSize) - pongWidth, rightPaddleY, rightPaddleHeight, -1))
  pong.value += 1;

  ballX += veloBallX;
  ballY += veloBallY;

  if (nbr == ping.value + pong.value)
    alreadyComputed = preColision();

}

const moovePaddles = () => {
  if (leftArrowUp && leftPaddleY > 1 - leftPaddleHeight)
    leftPaddleY -= leftPaddleSpeed * leftArrowUp;
  else if (leftArrowDown && leftPaddleY < pongHeight - 1)
    leftPaddleY += leftPaddleSpeed * leftArrowDown;

  if (rightArrowUp && rightPaddleY > 1 - rightPaddleHeight)
    rightPaddleY -= rightPaddleSpeed * rightArrowUp;
  else if (rightArrowDown && rightPaddleY < pongHeight - 1)
    rightPaddleY += rightPaddleSpeed * rightArrowDown;
}

function drawBall() {
  if (!ctx)
    return ;
  ctx.beginPath();
  console.log('drawBall' + '\nx = ' + ballX + '\ny = ' + ballY);
  ctx.arc(ballX/3, ballY/4, ballSize/3, 0, Math.PI * 2);
  ctx.fillStyle = 'white';  // Or any color
  ctx.fill();
  ctx.closePath();
}

function drawPaddle(x:number, y:number, width:number, height:number) {
  if (!ctx)
    return ;
  console.log('drawPaddle' + '\nx = ' + x + '\ny = ' + y + '\nwidth = ' + width + '\nheight = ' + height);
  ctx.beginPath();
  ctx.rect(x/3, y/4, width/3, height/4);
  ctx.fillStyle = 'blue';  // Or any color
  ctx.fill();
  ctx.closePath();
}


function gameLoop() {
    if (!ctx)
      return ;
    ctx.clearRect(0, 0, pongWidth, pongHeight);
    console.log('\n' + myCanvas.value + '\n');

    ballColision();
    
    moovePaddles();
  
    drawBall();
    drawPaddle(paddleOffset, leftPaddleY, leftPaddleWidth, leftPaddleHeight);
    console.log('paddleOffset = ' + paddleOffset + '\nleftPaddleY = ' + leftPaddleY + '\nleftPaddleWidth = ' + leftPaddleWidth + '\nleftPaddleHeight = ' + leftPaddleHeight + '\n-------\n');
    drawPaddle(pongWidth - (rightPaddleWidth + paddleOffset), rightPaddleY, rightPaddleWidth, rightPaddleHeight);
    retVeloBallX.value = veloBallX + 1;
    retVeloBallY.value = veloBallY + 1;
}

/*************HANDLE KEYS****************/

const handleKeyDown = (event: KeyboardEvent) => {
    console.log(event.key + ' keyDown\n');
if (event.key === 'ArrowRight')
    rightPaddleWidth += 10;
    if (event.key === 'ArrowLeft')
    leftPaddleWidth += 10;
  if (event.key === leftPlayerKeyUp) 
    leftArrowUp = 1;
  else if (event.key === leftPlayerKeyDown)
    leftArrowDown = 1;

  if (event.key === rightPlayerKeyUp)
    rightArrowUp = 1;
  else if (event.key === rightPlayerKeyDown)
    rightArrowDown = 1;
};

const handleKeyUp = (event: KeyboardEvent) => {
  if (event.key === leftPlayerKeyUp) {
  leftArrowUp = 0;
  } else if (event.key === leftPlayerKeyDown) {
  leftArrowDown = 0;
  }

  if (event.key === rightPlayerKeyUp) {
  rightArrowUp = 0;
  } else if (event.key === rightPlayerKeyDown) {
  rightArrowDown = 0;
  }
};


/********************************BLOCKS********************************/

interface Block {
  x: number;
  y: number;
  width: number;
  height: number;
  color: string;
  id: number;
}

class SolidBlock implements Block {
    isSolid: boolean;
    x: number;
    y: number;
    width: number;
    height: number;
    color: string;
    id: number;

    constructor(x: number, y: number, width: number, height: number, color: string, isSolid: boolean) {
        this.x = x;
        this.y = y;
        this.width = width;
        this.height = height;
        this.isSolid = isSolid;
        this.color = color;
        this.id = whatId++;
    }

 
}

class EffectBlock implements Block {
    effect: string;
    x: number;
    y: number;
    width: number;
    height: number;
    color: string;
    id: number;

    constructor(x: number, y: number, width: number, height: number, color: string, effect: string) {
        this.x = x;
        this.y = y;
        this.width = width;
        this.height = height;
        this.effect = effect;
        this.color = color;
        this.id = whatId++;
    }

    triggerEffect(): void {
      if (this.effect === "PING")
      {
        veloBallX *= -1.5;
        if (veloBallX > ballMaxSpeedX)
          veloBallX = ballMaxSpeedX;
      }
      if (this.effect === "SLOWn")
      {
        veloBallX /= 2;
        veloBallY /= 2;
      }

   
}
}

const checkBlockColi = (genX:number, genY:number, block: Block):boolean => {
if (Math.abs(genX - block.x) < blockWidth && Math.abs(genY - block.y) < blockHeight)
        return true;
    return false;
}

const isValidGen = (genX:number, genY:number):boolean => {
  for (let block of myBlocks)
    {
      if (checkBlockColi(genX, genY, block))  
        return (false);
    }
    return (true);
  }

const generateBlocks = () => {

  let genX = 0;
  let genY = 0;
  let j = 0;
  while ( j < 25)
  {
    genX = pongWidth/3 + pongWidth/3 * Math.random();
    genY = pongHeight*3/4 - pongHeight * Math.random()/2;
    if (isValidGen(genX, genY))
      break ;
    j++;
  }
  if (j == 25)
    return ;
  switch ((ping.value + pong.value) % 3)
  {
    case 0:
    {
      myBlocks.push(new EffectBlock(genX,
                            genY,
                            blockWidth, blockHeight, '#deec1c' , "PING"));
      break ;
    }
    case 1:
    {
      myBlocks.push(new EffectBlock(genX,
                            genY,
                            blockWidth, blockHeight, '#d24dff' , "SLOW"));
      break ;
    }
    case 2:
    {
      myBlocks.push(new SolidBlock(genX,
                            genY,
                            blockWidth, blockHeight, '#ff0000' , true));
      break ;
    }
  }


  
}

const removeBlock = (blockId: number) => {
    myBlocks = myBlocks.filter(block => block.id !== blockId);
}

/*********************************COMPUTED************************************/
const gameCanvasStyle = computed(() => ({
  '--pongWidth': '900px',
  '--pongHeight': '600px',
}));

onMounted(() => {
      if (myCanvas.value) {
        ctx = myCanvas.value.getContext('2d');
        if (ctx)
        {
            window.addEventListener('keydown', handleKeyDown);
            window.addEventListener('keyup', handleKeyUp);
            setInterval(gameLoop, 16);
        }
      }
    });

onUnmounted(() => {
  window.removeEventListener('keydown', handleKeyDown);
  window.removeEventListener('keyup', handleKeyUp);
});
  
return{
    myCanvas,
    gameCanvasStyle,
  scoreA,
  scoreB,
  line,
  retVeloBallX,
  retVeloBallY,
  startMatchSolo,
  restartMatch,
  startMatchMultiLocal,
  startNoPlayer,
  setBlocks,
  gameIsRunning,
  leftPlayerKeyUp,
  leftPlayerKeyDown,
  rightPlayerKeyUp,
  rightPlayerKeyDown,
  ping,
  pong,
  blockStatus,
};
}
},

  );

</script>
