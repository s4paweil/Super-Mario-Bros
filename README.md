# Super Mario Bros.

Classic Super Mario Bros. game implemented with Java for CS319-Object-Oriented Software Engineering course.

## About game

You can visit [wikipedia page](https://en.wikipedia.org/wiki/Super_Mario_Bros.) or [Super Mario wiki page](https://www.mariowiki.com/Super_Mario_Bros.) for detailed information about the game.

## Built With
* [Java](https://www.java.com/)

## In-game Screens

### Start Screen
![start screen](https://raw.githubusercontent.com/ahmetcandiroglu/1G.Super-Mario-Bros/master/docs/Screenshots/Start%20screen.png)

### Inside Game
![in game screen](https://raw.githubusercontent.com/ahmetcandiroglu/1G.Super-Mario-Bros/master/docs/Screenshots/In%20game%20screen.png)

### Pause Screen
![pause screen](https://raw.githubusercontent.com/ahmetcandiroglu/1G.Super-Mario-Bros/master/docs/Screenshots/Pause%20screen.png)

# Intentional Bugs

This version contains the following intentional Bugs:

## Bug 1
Enemies do not turn at collision with objects (easy to see on Map 2 - the Gumba at the start).

## Bug 2
Mario has evolved, he can jump infinitly often in the air now, atleast while on the upwards movement. He still can't jump while falling.
But he shouldn't be able to do multiple jumps at all.

## Bug 3
Whenever Mario gets a Coin from the ?-Blocks, he does get the Points for it BUT the Coin counter in the upper right corner does not increase.
Mario really needs the counter to work to get a 1UP! when hitting 100 Coins!






# Solution to Bugs

## Bug 1 - Enemies run through Objects
This Bug is pretty straightforward. Can be found/solved on a very short path, if the structure of the program ist known, or a slightly longer path.

Potential paths to Problem:
* GameEngine.init() -> MapManager() -> checkCollision() -> checkEnemyCollision
* GameEnige.init() -> start() -> run() -> gameLoop() -> checkCollision() -> mapManager.checkCollision() -> checkEnemyCollision()

### Bug 1 fixing solution
Inside MapManager.checkEnemyCollision():

    if (enemyBounds.intersects(brickBounds)) {
        enemy.setVelX(enemy.getVelX());
    }

should be corrected to

    if (enemyBounds.intersects(brickBounds)) {
        enemy.setVelX(-enemy.getVelX());
    }

## Bug 2 - Mario can jump infinitly often
To find the second bug is pretty easy aswell, to solve it is a bit harder.
The bug is located in Mario.jump(), but it is probably necessary to look at GameObject.updateLocation() aswell.
* GameEngine.receiveInput() -> Mario.jump()
* GameEngine.updateLocations() -> MapManager.updateLocations() -> Map.updateLocations() -> GameObject.updateLocations()

### Bug 2 fixing solution
Inside Mario.jump():

    public void jump(GameEngine engine) {
        if(!isFalling()){
            setJumping(true);
            setVelY(10);
            engine.playJump();
        }
    }

should be changed to:

    public void jump(GameEngine engine) {
        if(!isJumping() && !isFalling()){
            setJumping(true);
            setVelY(10);
            engine.playJump();
        }
    }


## Bug 3 - Coin counter
Path to the Bug - not that easy to find, unless you directly navigate to the Coin class.
The Bug is located in Coin.onTouch().
* GameEngine.run() -> GameEngine.gameLoop() -> GameEngine.checkCollisions() -> MapManager.checkCollisions() -> MapManager.checkPrizeContact() -> Prize.onTouch() - interface is implemented in Coin - Coin.onTouch()
Additionally the Mario class has to be checked for the Method
* Mario.acquireCoin()

### Bug 3 fixing solution
Inside Coin.onTouch():

    public void onTouch(Mario mario, GameEngine engine) {
        if(!acquired){
            acquired = true;
            mario.acquirePoints(point);
            engine.playCoin();
        }
    }

the following line should be addded:

    mario.acquireCoin();