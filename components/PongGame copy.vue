<template>
  <div class="game-container">

  <div class="info-container">
    <p>{{ "veloBall x-y " + veloBallX + " " + veloBallY }}</p>
    <p>{{ "Ping " + ping }}</p>
    <p>{{ "Pong " + pong}}</p>
  </div>

  <div class="button-container">
    <button @click="startMatchSolo" :disabled="gameIsRunning">Solo</button>
    <button @click="startMatchMultiLocal" :disabled="gameIsRunning">MultiplayerLocal</button>
    <button @click="startNoPlayer" :disabled="gameIsRunning">NoPlayer</button>
    <button @click="restartMatch" :disabled="!gameIsRunning">Restart</button>
    <button @click="setBlocks" :disabled="gameIsRunning">{{"BLOCKS " + blockStatus}}</button>
  </div>
  <div class="pong-container">

    <div class="input-container">
      <div class="left-input">
        <p>{{ leftPlayerKeyUp }}</p>
        <p>{{ leftPlayerKeyDown }}</p>
      </div>
    </div>

  <div class="pong" :style ="pongStyle">
   <div class="scores">
      <div class="scorePlayer">{{ "Score A : " + scoreA }}</div>
      <div class="scorePlayer">{{ "Score B : " + scoreB }}</div>
    </div>
    <!-- <<div
      v-for="block in myBlocks"
      :key="block.id"
      class="block"
      :style="{ top: block.y + 'px', left: block.x + 'px', width: block.width + 'px', height: block.height + 'px', backgroundColor: block.color}">
    </div>> -->
    <div class="paddle" :style="leftPaddleStyle"></div>
    <div class="paddle" :style="rightPaddleStyle"></div>
    <div class="ball" :style="ballStyle"></div>
  </div>

  <div class="input-container">
    <div class="right-input">
        <p>{{ rightPlayerKeyUp }}</p>
        <p>{{ rightPlayerKeyDown }}</p>
      </div>
    </div>

</div>
</div>

</template>

<style>




.block {
  position: absolute;
  z-index: 1; /* Ensure the block is positioned above the Pong game elements */
}

.info-container {
  position: absolute;
  top: 0;
  bottom: 5%;
  left: 1%;;
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
  position: absolute;
  display: flex;
  flex-direction: column;
  justify-content: flex-end;
  align-items: flex-start;
  margin-top: 5%;
  margin-right: 5%;
  padding-top: 120px; /* Space for the buttons. This should be greater than the height of the buttons */
}


.pong-container {
  overflow: hidden;
  position: absolute;
  top: 10%;
display: flex;
align-items: center;
}

.input-container {
display: flex;
justify-content: space-between;
align-items: center;

}

.left-input,
.right-input {
width: 150px;
}

.left-input {
text-align: right;
}

.right-input {
text-align: left;
}



.pong {
  position: relative;
  width: var(--pong-width, 900px);
  height: var(--pong-height, 600px);
  border: 1px solid #6b4d4d;
  overflow: visible;
}

.scores {
display: flex;
justify-content: center;
align-items: center;
height: 40px;
font-size: 24px;
color: #08225a;
text-align: center;
}

.scorePlayer {
flex: 1;
}

.paddle {
  position: absolute;
  width: var(--paddle-width, 10px);
  height: var(--paddle-height, 80px);
  top: var(--paddle-top, 0px);
  left: var(--paddle-left, 0px);
  background-color: #147f83;
}


.ball {
  position: absolute;
  top: var(--ball-top, 10px);
  left: var(--ball-left, 10px);
  width: var(--ball-width, 10px);
  height: var(--ball-height, 10px);
  background-color: rgba(247, 6, 166, 0.521);
  border-radius: 50%;
}

</style>

<script lang="ts">

import { defineComponent, onMounted,  computed } from 'vue';


export default defineComponent({


  data() {
  
  
  // GAME CONTAINER PARAMETERS
  let  pongWidth = 900;
  let   pongHeight = 600;

  // CONTROL PARAMETERS
  let rightPlayerKeyUp = 'ArrowUp';
  let rightPlayerKeyDown = 'ArrowDown';
  let leftPlayerKeyUp = 'w';
  let leftPlayerKeyDown = 's';

  // LEFT PADDLE PARAMETERS
  let leftPaddleWidth = 10;
  let leftPaddleHeight = 80;

  let leftPaddleY = pongHeight / 2 - leftPaddleHeight / 2;
  let leftPaddleSpeed = 12;


  // RIGHT PADDLE PARAMETERSSend a message.

  let rightPaddleWidth = 10;
  let rightPaddleHeight = 80;

  let rightPaddleY = pongHeight / 2 - rightPaddleHeight / 2;
  let rightPaddleSpeed = 12;


  // BALL PARAMETERS
  let ballSize = 10;
  let paddleOffset = 20;

  let gameTick = 16;

  let ballMaxSpeedX = 35;
  let ballMaxSpeedY = 10;
  let bounce = 25;

  let blockWidth = pongHeight * 0.05;
  let blockHeight = pongWidth * 0.05;

  let leftPaddleJustHit = 0;



  let rightPaddleJustHit = 0;

  //BALL PARAMETERS


  let ballX = pongWidth/2 - ballSize/2;
  let ballY = pongHeight/2 - ballSize/2;

  let ballStartSpeedX = -4;
  let ballStartSpeedY = 2;

  let veloBallX = 0;
  let veloBallY = 0;



  let ping = 0;
  let pong = 0;

  let scoreA = 0;
  let scoreB = 0;

  //BLOCKS PARAMETERS



  //VARIOUS PARAMETERSlet

  let whatId = 0;

  let gameIsRunning = 0;
  let gameIsBlocks = 0;
  
  let alreadyComputed = 0;
  let blockStatus = 'DISABLED';

  let leftArrowUp = 0;
  let leftArrowDown = 0;

  let rightArrowUp = 0;
  let rightArrowDown = 0;



  const leftPaddleStyle = computed(() => ({
  '--paddle-top': `${leftPaddleY}px`,
  '--paddle-left': `${paddleOffset}px`,
  '--paddle-width': `${leftPaddleWidth}px`,
  '--paddle-height': `${leftPaddleHeight}px`,
}));

const rightPaddleStyle = computed(() => ({
  '--paddle-top': `${rightPaddleY}px`,
  '--paddle-left': `${pongWidth - (leftPaddleWidth + paddleOffset)}px`,
  '--paddle-width': `${rightPaddleWidth}px`,
  '--paddle-height': `${rightPaddleHeight}px`,
}));

const pongStyle = computed(() => ({
  '--pong-width': `${pongWidth}px`,
  '--pong-height': `${pongHeight}px`,
}));

const ballStyle = computed(() => ({
  '--ball-top': `${ballY}px`,
  '--ball-left': `${ballX}px`,
}));

console.log("setup\n")
let myBlocks = [];

  return {pongWidth, pongHeight, rightPlayerKeyUp, rightPlayerKeyDown, leftPlayerKeyUp, leftPlayerKeyDown, leftPaddleWidth,
    leftPaddleHeight, leftPaddleY, leftPaddleSpeed, rightPaddleWidth, rightPaddleHeight, rightPaddleY, rightPaddleSpeed, ballSize,
    paddleOffset, gameTick, ballMaxSpeedX, ballMaxSpeedY, bounce, blockWidth, blockHeight, leftPaddleJustHit, rightPaddleJustHit,
    ballX, ballY, ballStartSpeedX, ballStartSpeedY, veloBallX, veloBallY, ping, pong, scoreA, scoreB, whatId, gameIsRunning,
    gameIsBlocks, alreadyComputed, blockStatus, leftArrowUp, leftArrowDown, rightArrowUp, rightArrowDown,
    leftPaddleStyle, rightPaddleStyle, pongStyle, ballStyle, myBlocks};    

},


  /***********************GAME FUNCTIONS***********************/

  /***********************START GAME***********************/
  SetUp: {
  
  startMatchSolo() {
    if (!this.gameIsRunning) {
      const rand = Math.random() * 2 - 1;
      this.veloBallX = rand + (rand / Math.abs(rand)) * 2;
      this.veloBallY = Math.random() * 6 - 3;

      this.ballX = this.pongWidth / 2 - this.ballSize / 2;
      this.ballY = this.pongHeight / 2 - this.ballSize / 2;

      this.rightPaddleHeight = this.pongHeight;
      this.rightPaddleY = 0;

      this.rightPlayerKeyDown = '';
      this.rightPlayerKeyUp = '';

      this.gameIsRunning = 1;
    }
  },

  startNoPlayer() {
    if (!this.gameIsRunning) {
      const rand = Math.random() * 2 - 1;
      this.veloBallX = rand + (rand / Math.abs(rand)) * 2;
      this.veloBallY = Math.random() * 6 - 3;

      this.ballX = this.pongWidth / 2 - this.ballSize / 2;
      this.ballY = this.pongHeight / 2 - this.ballSize / 2;

      this.rightPaddleHeight = this.pongHeight;
      this.rightPaddleY = 0;

      this.rightPlayerKeyDown = '';
      this.rightPlayerKeyUp = '';

      this.leftPaddleHeight = this.pongHeight;
      this.leftPaddleY = 0;

      this.leftPlayerKeyDown = '';
      this.leftPlayerKeyUp = '';

      this.gameIsRunning = 1;
    }
  },

  startMatchMultiLocal() {
    if (!this.gameIsRunning) {
      const rand = Math.random() * 2 - 1;
      this.veloBallX = rand + (rand / Math.abs(rand)) * 2;
      this.veloBallY = Math.random() * 6 - 3;

      this.ballX = this.pongWidth / 2 - this.ballSize / 2;
      this.ballY = this.pongHeight / 2 - this.ballSize / 2;

      this.gameIsRunning = 1;
    }
  },

  restartMatch() {
    if (this.gameIsRunning) {
      this.scoreA = 0;
      this.scoreB = 0;

      this.veloBallX = 0;
      this.veloBallY = 0;

      this.rightPaddleHeight = 80;
      this.rightPaddleY = this.pongHeight / 2 - this.rightPaddleHeight / 2;

      this.rightPlayerKeyDown = 'ArrowDown';
      this.rightPlayerKeyUp = 'ArrowUp';

      this.leftPaddleHeight = 80;
      this.leftPaddleY = this.pongHeight / 2 - this.leftPaddleHeight / 2;

      this.leftPlayerKeyDown = 's';
      this.leftPlayerKeyUp = 'w';

      this.ballX = this.pongWidth / 2 - this.ballSize / 2;
      this.ballY = this.pongHeight / 2 - this.ballSize / 2;

      this.ping = 0;
      this.pong = 0;

      this.gameIsRunning = 0;

      myBlocks = [];
    }
  },

/***********************BALL COLISIONS***********************/

ballWallCollision() {
  if (this.ballY <= 0 )
    this.veloBallY = Math.abs(this.veloBallY);
  else if (this.ballY >= this.pongHeight - this.ballSize)
    this.veloBallY = -Math.abs(this.veloBallY);

  if (this.ballX <= 1)
    {
      this.scoreB += 1;
      this.ballX = this.pongWidth/2 - this.ballSize/2;
      this.ballY = this.pongHeight/2 - this.ballSize/2;
      this.veloBallX = (2 + Math.random());
      this.veloBallY = (Math.random() * 6) - 3;
    }
  if (this.ballX >= this.pongWidth - this.ballSize - 1)
    {
      this.scoreA += 1;
      this.ballX = this.pongWidth/2 - this.ballSize/2;
      this.ballY = this.pongHeight/2 - this.ballSize/2;
      this.veloBallX = -(2 + Math.random());
      this.veloBallY = (Math.random() * 6) - 3;
    }
},

ballPaddleCollision(paddleX:number, paddleY:number, paddleHeight:number, sign:number) {
  if (sign * this.ballX <= paddleX) {
    if (this.ballY >= paddleY && this.ballY <= paddleY + paddleHeight) {
      this.veloBallX = sign * (Math.abs(this.veloBallX) + ((this.ballY - paddleY) / (paddleHeight / 2)));
      if (Math.abs(this.veloBallX) >= this.ballMaxSpeedX) {
        this.veloBallX = this.ballMaxSpeedX * sign;
      }
      this.veloBallY = ((this.ballY - (paddleY + paddleHeight / 2)) / (paddleHeight / 2)) * this.bounce + Math.random();
      if (this.gameIsBlocks && (this.ping + this.pong) % 1 < 5) {
        generateBlocks();
      }
      return 1;
    }
  }
  return 0;
},

ballBlockCollision() {
  for (let block of myBlocks) {
    if (this.ballX <= block.x + block.width && this.ballX + this.ballSize >= block.x) {
      if (this.ballY <= block.y + block.height && this.ballY + this.ballSize >= block.y) {
          block.triggerEffect();
        removeBlock(block.id);
      }
    }
  }
},

preCollision() {
  const oldVeloX = this.veloBallX;
  const oldVeloY = this.veloBallY;

  if (ballPaddleCollision(this.paddleOffset + this.leftPaddleWidth, this.leftPaddleY, this.leftPaddleHeight, 1) ||
    ballPaddleCollision((this.paddleOffset + this.rightPaddleWidth + this.ballSize) - this.pongWidth, this.rightPaddleY, this.rightPaddleHeight, -1)) {
    if (this.ballX < 200) {
      this.ping += 1;
      this.ballX = this.paddleOffset + this.leftPaddleWidth + this.ballSize / 2 + 1;
    } else {
      this.pong += 1;
      this.ballX = this.pongWidth - (this.paddleOffset + this.ballSize + this.rightPaddleWidth + 1);
    }
    return 1;
  }
  this.veloBallX = oldVeloX;
  this.veloBallY = oldVeloY;
  return 0;
},

ballCollision() {
  if (this.alreadyComputed) {
    this.alreadyComputed = 0;
    this.ballX += this.veloBallX;
    this.ballY += this.veloBallY;
    return;
  }
  ballWallCollision();
  ballBlockCollision();
  const nbr = this.ping + this.pong;
  if (ballPaddleCollision(this.paddleOffset + this.leftPaddleWidth, this.leftPaddleY, this.leftPaddleHeight, 1)) {
    this.ping += 1;
  } else if (ballPaddleCollision((this.paddleOffset + this.rightPaddleWidth + this.ballSize) - this.pongWidth, this.rightPaddleY, this.rightPaddleHeight, -1)) {
    this.pong += 1;
  }

  this.ballX += this.veloBallX;
  this.ballY += this.veloBallY;

  if (nbr == this.ping + this.pong) {
    this.alreadyComputed = preCollision();
  }
}

movePaddles() {
  if (this.leftArrowUp && this.leftPaddleY > 1 - this.leftPaddleHeight) {
    this.leftPaddleY -= this.leftPaddleSpeed * this.leftArrowUp;
  } else if (this.leftArrowDown && this.leftPaddleY < this.pongHeight - 1) {
    this.leftPaddleY += this.leftPaddleSpeed * this.leftArrowDown;
  }

  if (this.rightArrowUp && this.rightPaddleY > 1 - this.rightPaddleHeight) {
    this.rightPaddleY -= this.rightPaddleSpeed * this.rightArrowUp;
  } else if (this.rightArrowDown && this.rightPaddleY < this.pongHeight - 1) {
    this.rightPaddleY += this.rightPaddleSpeed * this.rightArrowDown;
  }
}

setBallPosition(x:number, y:number)
{
  this.veloBallX= x;
  this.veloBallY = y;
}

setBallVelo(multX:number, multY:number)
{
  this.veloBallX *= multX;
  this.veloBallY *= multY;
}

gameLoop() {
  ballCollision();
  movePaddles();
},

/***********************KEYBOARD EVENTS***********************/

handleKeyDown(event: KeyboardEvent){
  if (event.key === this.leftPlayerKeyUp) {
    this.leftArrowUp = 1;
  } else if (event.key === this.leftPlayerKeyDown) {
    this.leftArrowDown = 1;
  }

  if (event.key === this.rightPlayerKeyUp) {
    this.rightArrowUp = 1;
  } else if (event.key === this.rightPlayerKeyDown) {
    this.rightArrowDown = 1;
  }
},

handleKeyUp(event: KeyboardEvent) {
  if (event.key === this.leftPlayerKeyUp) {
    this.leftArrowUp = 0;
  } else if (event.key === this.leftPlayerKeyDown) {
    this.leftArrowDown = 0;
  }

  if (event.key === this.rightPlayerKeyUp) {
    this.rightArrowUp = 0;
  } else if (event.key === this.rightPlayerKeyDown) {
    this.rightArrowDown = 0;
  }
},

/***********************BLOCKS***********************/


class block {
  effect:string;
  x:number;
  y:number;
  width:number;
  height:number;
  color:string;
  id:number;

  constructor(x:number, y:number, width:number, height:number, color:string, effect:string, id:number) {
    this.x = x;
    this.y = y;
    this.width = width;
    this.height = height;
    this.effect = effect;
    this.color = color;
    this.id = id;
  }

  triggerEffect() {
    if (this.effect === "PING") {
      return;
    }
    if (this.effect === "SLOWn") {
      setBallVelo(-1/1.5, -1/1.5);
    }
    if (this.effect === "TP") {
      for (let block of myBlocks) {
          setBallPosition(block.x + block.width / 2,block.y + block.height / 2);
          removeBlock(block.id);
          return;
      }
    }
    if (this.effect === "SOLID") {
      setBallVelo(-1, 1);
    }
  }
}


checkBlockCollision(genX:number, genY:number, block:block) {
  if (Math.abs(genX - block.x) < (this.blockWidth + 5) && Math.abs(genY - block.y) < this.blockHeight + 5) {
    return 1;
  }
  return 0;
},

isValidGen = (genX:number, genY:number) {
  for (let block of myBlocks) {
    if (checkBlockCollision(genX, genY, block)) {
      return 0;
    }
  }
  return 1;
},

generateBlocks() {
  let genX = 0;
  let genY = 0;
  let j = 0;
  while (j < 25) {
    genX = this.pongWidth * 2 / 3 - this.pongWidth / 3 * Math.random();
    genY = this.pongHeight - this.blockHeight - (this.pongHeight - 2 * this.blockHeight) * Math.random();
    genX -= genX % this.blockWidth;
    genY -= genY % this.blockHeight;

    if (isValidGen(genX, genY)) {
      break;
    }
    j++;
  }
  if (j === 25) {
    return;
  }
  switch ((Math.floor(100 * Math.random())) % 4) {
    case 0: {
      myBlocks.push(new block(genX, genY, this.blockWidth, this.blockHeight, '#ff0000', "SOLID", this.whatId++));
      break;
    }
    case 1: {
      myBlocks.push(new block(genX, genY, this.blockWidth, this.blockHeight, '#659acf', "SLOW", this.whatId++));
      break;
    }
    case 2: {
      myBlocks.push(new block(genX, genY, this.blockWidth, this.blockHeight, '#deec1c', "PING", this.whatId++));
      break;
    }
    case 3: {
      myBlocks.push(new block(genX, genY, this.blockWidth, this.blockHeight, '#5a0899', "TP", this.whatId++));
      break;
    }
  }
},

removeBlock(blockId:number){
  myBlocks = myBlocks.filter(block => block.id !== blockId);
},

/***********************COMPUTED***********************/


const leftPaddleStyle = computed(() => ({
  '--paddle-top': `${this.leftPaddleY}px`,
  '--paddle-left': `${this.paddleOffset}px`,
  '--paddle-width': `${this.leftPaddleWidth}px`,
  '--paddle-height': `${this.leftPaddleHeight}px`,
}));

const rightPaddleStyle = computed(() => ({
  '--paddle-top': `${this.rightPaddleY}px`,
  '--paddle-left': `${this.pongWidth - (this.rightPaddleWidth + this.paddleOffset)}px`,
  '--paddle-width': `${this.rightPaddleWidth}px`,
  '--paddle-height': `${this.rightPaddleHeight}px`,
}));

const pongStyle = computed(() => ({
  '--pong-width': `${this.pongWidth}px`,
  '--pong-height': `${this.pongHeight}px`,
}));

const ballStyle = computed(() => ({
  '--ball-top': `${this.ballY}px`,
  '--ball-left': `${this.ballX}px`,
}));

const blocks = computed(() => myBlocks);

onMounted(() => {
  console.log("pongStyle: this.pongWidth = " + this.pongWidth + " this.pongHeight = " + this.pongHeight)
  setInterval(gameLoop, this.gameTick);
  window.addEventListener('keydown', handleKeyDown);
  window.addEventListener('keyup', handleKeyUp);
});





return {

  startMatchSolo,
  restartMatch,
  startMatchMultiLocal,
  startNoPlayer,
  leftPaddleStyle,
  rightPaddleStyle,
  pongStyle,
  ballStyle,

};
}
},

);
</script>




