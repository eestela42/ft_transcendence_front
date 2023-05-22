<!-- 
 <script lang = 'ts'>
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

  let myBlocks  = [];


  //VARIOUS PARAMETERS

  let whatId = 0;

  let gameIsRunning = 0;
  let gameIsBlocks = 0;
  
  let alreadyComputed = 0;
  let blockStatus = 'DISABLED';

  let leftArrowUp = 0;
  let leftArrowDown = 0;

  let rightArrowUp = 0;
  let rightArrowDown = 0;

  let x = 1;
  let line = '';

  return {pongWidth, pongHeight, rightPlayerKeyUp, rightPlayerKeyDown, leftPlayerKeyUp, leftPlayerKeyDown, leftPaddleWidth, leftPaddleHeight, leftPaddleY, leftPaddleSpeed, rightPaddleWidth, rightPaddleHeight, rightPaddleY, rightPaddleSpeed, ballSize, paddleOffset, gameTick, ballMaxSpeedX, ballMaxSpeedY, bounce, blockWidth, blockHeight, leftPaddleJustHit, rightPaddleJustHit, ballX, ballY, ballStartSpeedX, ballStartSpeedY, veloBallX, veloBallY, ping, pong, scoreA, scoreB, myBlocks, whatId, gameIsRunning, gameIsBlocks, alreadyComputed, blockStatus, leftArrowUp, leftArrowDown, rightArrowUp, rightArrowDown, x, line};    

} -->