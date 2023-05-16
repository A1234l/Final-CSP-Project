<script>
var gun, gunImg;
var trex, trexImg, trexImg2;
var ammo, ammoImg;
var enemy, bomb, enemyImg, enemyGun, enemyGunImg, bombImg, Ebullet, EBulletImg;
var ground;
var score;
var ammoG, bombG, EbulletG, enemyG, enemyGunG;
var gameState;
var explodeImg;
var hitPoints;

gameState = 'play';

score = 0;

hitPoints = 0;

function preload() {
  gunImg = loadAnimation('Nerf_Gun.png');
  trexImg = loadAnimation('Trex.png', 'TrexRun.png', 'TrexRun2.png');
  trexImg2 = loadAnimation('TrexCollided.png');
  enemyImg = loadAnimation('enemy.png');
  bombImg = loadAnimation('bombImage.png');
  // EBulletImg = loadAnimation('enemyBullet.png');
  ammoImg = loadAnimation('Bullet.png');
  explodeImg = loadAnimation('Explosion.png');
  // enemyGunImg = loadAnimation('Enemy_Gun.png');
}

function setup() {
  createCanvas(600, 300);

  trex = createSprite(35, 220, 10, 10);
  trex.addAnimation('trex', trexImg);
  trex.addAnimation('trexDead', trexImg2);
  trex.scale = 0.6;

  gun = createSprite(40, 300, 10, 10);
  gun.addAnimation('gun', gunImg);
  gun.scale = 0.1;

  ground = createSprite(600, 290, 1200, 10);
  ground.shapeColor = 'blue';
  ground.velocityX = -4;

  ammoG = createGroup();
  bombG = createGroup();
  enemyG = createGroup();
  // enemyGunG = createGroup();
}

function draw() {
  background('white');
  
  // console.log(hitPoints);

  drawSprites();

  trex.setCollider('circle', 0, 0, 40);

  gun.y = trex.y + 5;



  trex.velocityY = trex.velocityY + 0.4;
  trex.collide(ground);

  if (ground.x < ground.x / 2) {
    ground.x = 600;
  }


  if (gameState === 'play') {
    makeBomb();
    makeAlien();
    // alienGun();
    score = score + Math.round(frameCount / 100);
    
  if (keyDown('up') && trex.y > 220) {
    trex.velocityY = -9;
  }
    
  if (keyDown('space')) {
    spawnBullet();
  }
  }

  var bombLength = bombG.length;
  for (var i = 0; i < bombLength; i++) {
    if (ammoG.isTouching(bombG.get(i))) {
      bombG.get(i).visible = false;
      // ammoG.destroyEach();
      // score=score+1;
    }


    if (trex.isTouching(bombG.get(i)) && bombG.get(i).visible === true) {
      bombG.get(i).changeAnimation('blow', explodeImg);
      bombG.get(i).scale = 0.5;
      ground.velocityX = 0;
      gameState = 'end';
    }
  }

    var alienLength = enemyG.length;
  for (var e = 0; e < alienLength; e++) {
    if (ammoG.isTouching(enemyG.get(e))) {
      // enemyG.get(e).visible = false;
      hitPoints = hitPoints+1;
    }
    if(hitPoints === 30 && ammoG.isTouching(enemyG.get(e))){
      enemyG.get(e).visible = false;
      hitPoints = 0;
    }
    if (trex.isTouching(enemyG.get(e)) && enemyG.get(e).visible === true) {
      gameState = 'end';
    }
  }
  
  if (gameState === 'end') {
    trex.changeAnimation('trexDead', trexImg2);
    trex.velocityY = 0;
    bombG.setLifetimeEach(-1);
    ammoG.destroyEach();
    bombG.setVelocityXEach(0);
    ground.velocityX = 0;
    enemyG.setLifetimeEach(-1);
    enemyG.setVelocityXEach(0);
    // enemyGunG.setVelocityXEach(0);
    // enemyGunG.setLifetimeEach(-1);

    fill('black');
    textSize(40);
    text('Game Over', 200, 100);

    // score = score;
  }

  fill('black');
  textSize(18);
  text('Score: ' + score, 470, 30);
}
function spawnBullet() {
  if (World.frameCount % 5 === 0) {
    ammo = createSprite(100, 220, 10, 10);
    ammo.addAnimation('bullet', ammoImg);
    ammo.lifetime = 200;
    ammo.velocityX = 30;
    ammo.scale = 0.05;
    ammo.y = gun.y - 8;
    ammo.x = gun.x + 25;
    ammoG.add(ammo);
  }
}

function makeBomb() {
  if (World.frameCount % 60 === 0) {
    bomb = createSprite(650, Math.round(random(250, 150)), 10, 10);
    bomb.addAnimation('bomb', bombImg);
    bomb.addAnimation('blow', explodeImg);
    bomb.lifetime = 1300;
    bomb.velocityX = -(frameCount/160);
    bomb.scale = 0.05;
    bombG.add(bomb);
  }
}

function makeAlien(){
  if(World.frameCount % 120 === 0){
    enemy = createSprite(610,Math.round(random(170,280)),10,10);
    enemy.addAnimation('alien',enemyImg);
    enemy.scale = 0.12;
    enemy.velocityX = -(frameCount/180);
    enemy.lifetime = 1300;
    enemyG.add(enemy);
  }
}
</script>