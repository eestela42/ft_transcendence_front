<!-- <template>
  <div class="game-container">

  <div class="info-container">
    <p>{{ "veloBall x-y " + veloballX.value.value + " " + veloballY.value.value }}</p>
    <p>{{ "ping.value " + ping.value }}</p>
    <p>{{ "pong.value.value " + pong.value.value}}</p>
  </div>

  <div class="button-container">
    <button @click="startMatchSolo" :disabled="gameIsRunning.value">Solo</button>
    <button @click="startMatchMultiLocal" :disabled="gameIsRunning.value">MultiplayerLocal</button>
    <button @click="startNoPlayer" :disabled="gameIsRunning.value">NoPlayer</button>
    <button @click="restartMatch" :disabled="!gameIsRunning.value">Restart</button>
    <button @click="setBlocks" :disabled="gameIsRunning.value">{{"BLOCKS " + blockStatus.value}}</button>
  </div>
  <div class="pong.value.value-container">

    <div class="input-container">
      <div class="left-input">
        <p>{{ leftPlayerKeyUp.value }}</p>
        <p>{{ leftPlayerKeyDown.value }}</p>
      </div>
    </div>

  <div class="pong.value.value">
   <div class="scores">
      <div class="scorePlayer">{{ "Score A : " + scoreA.value }}</div>
      <div class="scorePlayer">{{ "Score B : " + scoreB.value }}</div>
    </div>
    <div
      v-for="block in myBlocks.value"
      :key="block.id"
      class="block"
      :style="{ top: block.y + 'px', left: block.x + 'px', width: block.width + 'px', height: block.height + 'px', backgroundColor: block.color}">
    </div>
    <div class="leftPaddleStyle"></div>
    <div class="rightPaddleStyle"></div>
    <div class="ball"></div>
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
  z-index: 1; /* Ensure the block is positioned above the pong.value.value game elements */
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
  position: absolute;
  display: flex;
  flex-direction: column;
  justify-content: flex-end;
  align-items: flex-start;
  margin-top: 5%;
  margin-right: 5%;
  padding-top: 120px; /* Space for the buttons. This should be greater than the height of the buttons */
}


.pong.value.value-container {
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



.pong.value.value {
  position: relative;
  width: pong.value.valueWidth.value + 'px';
  height: pong.value.valueHeight.value + 'px';
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
  height: var(--paddle-height, 10px);
  background-color: #147f83;
}
.leftPaddleStyle {
  left: paddleOffset.value + 'px';
  top: leftPaddleY + 'px';
  width: leftPaddleWidth.value + 'px';
  height: leftPaddleHeight.value + 'px';
}

.rightPaddleStyle {
  right: paddleOffset.value + 'px';
  top: rightPaddleY.value + 'px';
  width: rightPaddleWidth + 'px';
  height: rightPaddleHeight.value + 'px';
}

.ball {
  position: absolute;
  top: ballY.value + 'px';
  left: ballX.value + 'px';
  width: ballSize.value + 'px';
  height: ballSize.value + 'px';
  background-color: rgba(247, 6, 166, 0.521);
  border-radius: 50%;
}

</style>

<script lang="ts">

import { defineComponent,onMounted,  computed } from 'vue';
import {  } from 'fichier.vue';


export default defineComponent({

  import {
  SetpongWidth,
  SetpongHeight,
  SetRightPlayerKeyUp,
  SetRightPlayerKeyDown,
  SetleftPlayerKeyUp,
  SetleftPlayerKeyDown,
  SetleftPaddleWidth,
  SetleftPaddleHeight,
  SetLeftPaddleY,
  SetleftPaddleSpeed,
  SetRightPaddleWidth,
  SetrightPaddleHeight,
  SetrightPaddleY,
  SetrightPaddleSpeed,
  SetballSize,
  SetpaddleOffset,
  ballMaxSpeedX,
  ballMaxSpeedY,
  gameTick,
  SetBounce,
  SetBlockWidth,
  SetBlockHeight,
} from './pongSettings.ts'; 

export default defineComponent({




    setup() {

//GAME CONTAINER PARAMETERS
const pongWidth = ref(SetpongWidth);
const pongHeight = ref(SetpongHeight);

//CONTROL PARAMETERS
const rightPlayerKeyUp = ref(SetRightPlayerKeyUp);
const rightPlayerKeyDown = ref(SetRightPlayerKeyDown);

const leftPlayerKeyUp = ref(SetleftPlayerKeyUp);
const leftPlayerKeyDown = ref(SetleftPlayerKeyDown);

//LEFT PADDLE PARAMETERS
const bounce = ref(SetBounce);

const leftPaddleWidth = ref(SetleftPaddleWidth);
const leftPaddleHeight = ref(SetleftPaddleHeight);

const leftPaddleY = ref(SetLeftPaddleY);
const leftPaddleSpeed = ref(SetleftPaddleSpeed);

const leftPaddleJustHit = ref(false);

//RIGHT PADDLE PARAMETERS
const rightPaddleWidth = ref(SetRightPaddleWidth);
const rightPaddleHeight = ref(SetrightPaddleHeight);

const rightPaddleY = ref(SetrightPaddleY);
const rightPaddleSpeed = ref(SetrightPaddleSpeed);

const rightPaddleJustHit = ref(false);

//BALL PARAMETERS
const ballSize = ref(SetballSize);
const paddleOffset = ref(SetpaddleOffset);

const ballX = ref(pongWidth.value/2 - ballSize.value/2);
const ballY = ref(pongHeight.value/2 - ballSize.value/2);

const ballStartSpeedX = -4;
const ballStartSpeedY = 2;

const veloballX = ref(0);
const veloballY = ref(0);



const ping = ref(0);
const pong = ref(0);

const scoreA = ref(0);
const scoreB = ref(0);

//BLOCKS PARAMETERS

const myBlocks = ref([]);
const blockWidth = SetBlockWidth;
const blockHeight = SetBlockHeight;

//VARIOUS PARAMETERS

const whatId = ref(0);

const gameIsRunning = ref(false);
const gameIsBlocks = ref(false);
const blockStatus = ref('DISABLED');

const alreadyComputed = ref(false);

const leftArrowUp = ref(0);
const leftArrowDown = ref(0);

const rightArrowUp = ref(0);
const rightArrowDown = ref(0);

  /***********************GAME FUNCTIONS***********************/

  /***********************START GAME***********************/
  
  const startMatchSolo = () => {
    if (!gameIsRunning.value) {
      const rand = Math.random() * 2 - 1;
      veloballX.value.value = rand + (rand / Math.abs(rand)) * 2;
      veloballY.value.value = Math.random() * 6 - 3;

      ballX.value = pong.value.valueWidth.value / 2 - ballSize.value / 2;
      ballY.value = pong.value.valueHeight.value / 2 - ballSize.value / 2;

      rightPaddleHeight.value = pong.value.valueHeight.value;
      rightPaddleY.value = 0;

      rightPlayerKeyDown = '';
      rightPlayerKeyUp = '';

      gameIsRunning.value = 1;
    }
  };

  const startNoPlayer = () => {
    if (!gameIsRunning.value) {
      const rand = Math.random() * 2 - 1;
      veloballX.value.value = rand + (rand / Math.abs(rand)) * 2;
      veloballY.value.value = Math.random() * 6 - 3;

      ballX.value = pong.value.valueWidth.value / 2 - ballSize.value / 2;
      ballY.value = pong.value.valueHeight.value / 2 - ballSize.value / 2;

      rightPaddleHeight.value = pong.value.valueHeight.value;
      rightPaddleY.value = 0;

      rightPlayerKeyDown = '';
      rightPlayerKeyUp = '';

      leftPaddleHeight.value = pong.value.valueHeight.value;
      leftPaddleY = 0;

      leftPlayerKeyDown.value = '';
      leftPlayerKeyUp.value = '';

      gameIsRunning.value = 1;
    }
  };

  const startMatchMultiLocal = () => {
    if (!gameIsRunning.value) {
      const rand = Math.random() * 2 - 1;
      veloballX.value.value = rand + (rand / Math.abs(rand)) * 2;
      veloballY.value.value = Math.random() * 6 - 3;

      ballX.value = pong.value.valueWidth.value / 2 - ballSize.value / 2;
      ballY.value = pong.value.valueHeight.value / 2 - ballSize.value / 2;

      gameIsRunning.value = 1;
    }
  };

  const restartMatch = () => {
    if (gameIsRunning.value) {
      scoreA.value = 0;
      scoreB.value = 0;

      veloballX.value.value = 0;
      veloballY.value.value = 0;

      rightPaddleHeight.value = 80;
      rightPaddleY.value = pong.value.valueHeight.value / 2 - rightPaddleHeight.value / 2;

      rightPlayerKeyDown = 'ArrowDown';
      rightPlayerKeyUp = 'ArrowUp';

      leftPaddleHeight.value = 80;
      leftPaddleY = pong.value.valueHeight.value / 2 - leftPaddleHeight.value / 2;

      leftPlayerKeyDown.value = 's';
      leftPlayerKeyUp.value = 'w';

      ballX.value = pong.value.valueWidth.value / 2 - ballSize.value / 2;
      ballY.value = pong.value.valueHeight.value / 2 - ballSize.value / 2;

      ping.value = 0;
      pong.value.value = 0;

      gameIsRunning.value = 0;

      myBlocks.value = [];
    }
  };

/***********************BALL COLISIONS***********************/

const ballWallCollision = () => {
  if (ballY.value <= 0 )
    veloballY.value.value = Math.abs(veloballY.value.value);
  else if (ballY.value >= pong.value.valueHeight.value - ballSize.value)
    veloballY.value.value = -Math.abs(veloballY.value.value);

  if (ballX.value <= 1)
    {
      scoreB.value += 1;
      ballX.value = pong.value.valueWidth.value/2 - ballSize.value/2;
      ballY.value = pong.value.valueHeight.value/2 - ballSize.value/2;
      veloballX.value.value = (2 + Math.random());
      veloballY.value.value = (Math.random() * 6) - 3;
    }
  if (ballX.value >= pong.value.valueWidth.value - ballSize.value - 1)
    {
      scoreA.value += 1;
      ballX.value = pong.value.valueWidth.value/2 - ballSize.value/2;
      ballY.value = pong.value.valueHeight.value/2 - ballSize.value/2;
      veloballX.value.value = -(2 + Math.random());
      veloballY.value.value = (Math.random() * 6) - 3;
    }
};

const ballPaddleCollision = (paddleX, paddleY, paddleHeight, sign) => {
  if (sign * ballX.value <= paddleX) {
    if (ballY.value >= paddleY && ballY.value <= paddleY + paddleHeight) {
      veloballX.value.value = sign * (Math.abs(veloballX.value.value) + ((ballY.value - paddleY) / (paddleHeight / 2)));
      if (Math.abs(veloballX.value.value) >= ballMaxSpeedX) {
        veloballX.value.value = ballMaxSpeedX * sign;
      }
      veloballY.value.value = ((ballY.value - (paddleY + paddleHeight / 2)) / (paddleHeight / 2)) * bounce + Math.random();
      if (gameIsBlocks.value && (ping.value + pong.value.value) % 1 < 5) {
        generateBlocks();
      }
      return 1;
    }
  }
  return 0;
};

const ballBlockCollision = () => {
  for (let block of myBlocks.value) {
    if (ballX.value <= block.x + block.width && ballX.value + ballSize.value >= block.x) {
      if (ballY.value <= block.y + block.height && ballY.value + ballSize.value >= block.y) {
        if (block instanceof SolidBlock && block.isSolid) {
          veloballX.value.value = -veloballX.value.value;
        } else if (block instanceof EffectBlock) {
          block.triggerEffect();
        }
        removeBlock(block.id);
      }
    }
  }
};

const preCollision = () => {
  const oldVeloX = veloballX.value.value;
  const oldVeloY = veloballY.value.value;

  if (ballPaddleCollision(paddleOffset.value + leftPaddleWidth.value, leftPaddleY, leftPaddleHeight.value, 1) ||
    ballPaddleCollision((paddleOffset.value + rightPaddleWidth + ballSize.value) - pong.value.valueWidth.value, rightPaddleY.value, rightPaddleHeight.value, -1)) {
    if (ballX.value < 200) {
      ping.value += 1;
      ballX.value = paddleOffset.value + leftPaddleWidth.value + ballSize.value / 2 + 1;
    } else {
      pong.value.value += 1;
      ballX.value = pong.value.valueWidth.value - (paddleOffset.value + ballSize.value + rightPaddleWidth + 1);
    }
    return 1;
  }
  veloballX.value.value = oldVeloX;
  veloballY.value.value = oldVeloY;
  return 0;
};

const ballCollision = () => {
  if (alreadyComputed.value) {
    alreadyComputed.value = 0;
    ballX.value += veloballX.value.value;
    ballY.value += veloballY.value.value;
    return;
  }
  ballWallCollision();
  ballBlockCollision();
  const nbr = ping.value + pong.value.value;
  if (ballPaddleCollision(paddleOffset.value + leftPaddleWidth.value, leftPaddleY, leftPaddleHeight.value, 1)) {
    ping.value += 1;
  } else if (ballPaddleCollision((paddleOffset.value + rightPaddleWidth + ballSize.value) - pong.value.valueWidth.value, rightPaddleY.value, rightPaddleHeight.value, -1)) {
    pong.value.value += 1;
  }

  ballX.value += veloballX.value.value;
  ballY.value += veloballY.value.value;

  if (nbr == ping.value + pong.value.value) {
    alreadyComputed.value = preCollision();
  }
};

const movePaddles = () => {
  if (leftArrowUp.value && leftPaddleY > 1 - leftPaddleHeight.value) {
    leftPaddleY -= leftPaddleSpeed.value * leftArrowUp.value;
  } else if (leftArrowDown.value && leftPaddleY < pong.value.valueHeight.value - 1) {
    leftPaddleY += leftPaddleSpeed.value * leftArrowDown.value;
  }

  if (rightArrowUp.value && rightPaddleY.value > 1 - rightPaddleHeight.value) {
    rightPaddleY.value -= rightPaddleSpeed.value * rightArrowUp.value;
  } else if (rightArrowDown.value && rightPaddleY.value < pong.value.valueHeight.value - 1) {
    rightPaddleY.value += rightPaddleSpeed.value * rightArrowDown.value;
  }
};

const gameLoop = () => {
  ballCollision();
  movePaddles();
};

/***********************KEYBOARD EVENTS***********************/

const handleKeyDown = (event) => {
  if (event.key === leftPlayerKeyUp.value) {
    leftArrowUp.value = 1;
  } else if (event.key === leftPlayerKeyDown.value) {
    leftArrowDown.value = 1;
  }

  if (event.key === rightPlayerKeyUp) {
    rightArrowUp.value = 1;
  } else if (event.key === rightPlayerKeyDown) {
    rightArrowDown.value = 1;
  }
};

const handleKeyUp = (event) => {
  if (event.key === leftPlayerKeyUp.value) {
    leftArrowUp.value = 0;
  } else if (event.key === leftPlayerKeyDown.value) {
    leftArrowDown.value = 0;
  }

  if (event.key === rightPlayerKeyUp) {
    rightArrowUp.value = 0;
  } else if (event.key === rightPlayerKeyDown) {
    rightArrowDown.value = 0;
  }
};

/***********************BLOCKS***********************/


class SolidBlock {
  isSolid;
  x;
  y;
  width;
  height;
  color;
  id;

  constructor(x, y, width, height, color, isSolid) {
    x = x;
    y = y;
    width = width;
    height = height;
    isSolid = isSolid;
    color = color;
    id = whatId.value++;
  }
}

class EffectBlock {
  effect;
  x;
  y;
  width;
  height;
  color;
  id;

  constructor(x, y, width, height, color, effect) {
    x = x;
    y = y;
    width = width;
    height = height;
    effect = effect;
    color = color;
    id = whatId.value++;
  }

  triggerEffect() {
    if (effect === "ping.value") {
      veloballX.value.value *= -0.5;
      if (Math.abs(veloballX.value.value) > ballMaxSpeedX) {
        veloballX.value.value = ballMaxSpeedX;
      }
    }
    if (effect === "SLOWn") {
      veloballX.value.value /= 1.5;
      veloballY.value.value /= 1.5;
    }
    if (effect === "TP") {
      for (let block of myBlocks.value) {
        if (block instanceof EffectBlock && block.effect === "TP" && block.id !== id) {
          ballX.value = block.x + blockWidth / 2;
          ballY.value = block.y + blockHeight / 2;
          removeBlock(block.id);
          return;
        }
      }
    }
  }
}

const checkBlockCollision = (genX, genY, block) => {
  if (Math.abs(genX - block.x) < (blockWidth + 5) && Math.abs(genY - block.y) < blockHeight + 5) {
    return 1;
  }
  return 0;
};

const isValidGen = (genX, genY) => {
  for (let block of myBlocks.value) {
    if (checkBlockCollision(genX, genY, block)) {
      return 0;
    }
  }
  return 1;
};

const generateBlocks = () => {
  genX = 0;
  genY = 0;
  j = 0;
  while (j < 25) {
    genX = pong.value.valueWidth.value * 2 / 3 - pong.value.valueWidth.value / 3 * Math.random();
    genY = pong.value.valueHeight.value - blockHeight - (pong.value.valueHeight.value - 2 * blockHeight) * Math.random();
    genX -= genX % blockWidth;
    genY -= genY % blockHeight;
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
      myBlocks.value.push(new SolidBlock(genX, genY, blockWidth, blockHeight, '#ff0000', true));
      break;
    }
    case 1: {
      myBlocks.value.push(new EffectBlock(genX, genY, blockWidth, blockHeight, '#659acf', "SLOW"));
      break;
    }
    case 2: {
      myBlocks.value.push(new EffectBlock(genX, genY, blockWidth, blockHeight, '#deec1c', "ping.value"));
      break;
    }
    case 3: {
      myBlocks.value.push(new EffectBlock(genX, genY, blockWidth, blockHeight, '#5a0899', "TP"));
      break;
    }
  }
};

const removeBlock = (blockId) => {
  myBlocks.value = myBlocks.value.filter(block => block.id !== blockId);
};

/***********************COMPUTED***********************/




  setInterval(gameLoop, 16);
  window.addEventListener('keydown', handleKeyDown);
  window.addEventListener('keyup', handleKeyUp);



return {

  startMatchSolo,
  restartMatch,
  startMatchMultiLocal,
  startNoPlayer,


};
}
},

  );
</script>



 -->
