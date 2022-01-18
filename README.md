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
Mario has evolved, he can jump infinitly often in the air now... although he shouldn't...







# Solution to Bugs

## Bug 1
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

## Bug 2
To find the second bug is pretty easy aswell, to solve it is a bit harder.
The bug is located in Mario.jump(), but it is probably necessary to look at GameObject.updateLocation() aswell.
* GameEngine.receiveInput() -> Mario.jump()
* GameEngine.updateLocations() -> MapManager.updateLocations() -> Map.updateLocations() -> GameObject.updateLocations()

### Bug 2 fixing solution
Inside Mario.jump():

    public void jump(GameEngine engine) {
        if(true){
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

P.S. Had to add one line to GameObject.updateLocation() for the bug to work correct

    else if(jumping){
        velY = velY - gravityAcc;
        y = y - velY;
        falling = false;        // added for a bug to work
    }