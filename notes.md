<!DOCTYPE html>
<html lang="en">
  <head>
    <meta charset="UTF-8" />
    <meta name="viewport" content="width=device-width, initial-scale=1.0" />
    <title>Ball Fall Game</title>
  </head>
    <body>
        <section style="display: flex; justify-content: center">
            <div>
                <center>
                    <h1>Ball Fall Game</h1>
                </center>
                <canvas
                    id="canvas"
                    height="600"
                    width="600"
                    style="border: 1px solid black"
                >
                    The canvas is not supported!
                </canvas>
            </div>
        </section>
        <script>
            const canvas = document.getElementById("canvas");
            const ctx = canvas.getContext("2d");
            const canvasH = canvas.height;
            const canvasW = canvas.width;

            let ball = { x: 150, y: -10, w: 5}
            let platformH = 10
            let platformW = canvasW
            let platformDiff = 50
            let platformSpeed = 1
            let leftPressed = (rightPressed = false)
            let moveSpeed = 2
            let score = 0
            let interval = null
            let scoreInterval = null

            function randomHoleX() {
                return Math.floor(Math.random() * 270)
            }

            let platforms = [{ x:0 ,y: canvasH, holeX: randomHoleX(), holeW: 20}]

            drawBall()
            drawPlatforms()
            movePlatforms()
            navigateBall()

            scoreInterval = setInterval(() => {
                score++
            }, 1000)


            function holdAndDrop(closest) {
                if (ball.y > closest.y - ball.w) {
                    if (ball.x > closest.holeX && 
                    ball.x < closest.holeX + closest.holeW) {
                        ball.y += 1
                }} else {
                    ball.y = closest.y - ball.w;
                }
            }

            function drawBall(){
                if (leftPressed && ball.x - ball.r > 0) {
                    ball.x -= moveSpeed
                }
                if (rightPressed && ball.x + ball.r < canvasW){
                    ball.x += moveSpeed
                }

                ctx.beginPath();
                const img = new Image();
                img.src = "http://www.pngall.com/wp-content/uploads/5/Red-Ball-PNG.png";
                ctx.drawImage(img, ball.x, ball.y, ball.w, ball.w);
                ctx.closePath();
            }

            function movePlatforms(){
                let count = 0;
                if (interval) return
                interval = setInterval(() => {
                    checkGameOver()
                    if (count === Math.floor(platformDiff / platformSpeed)) {
                        if (platforms.length > 20) {
                            platforms.splice(0, 4)
                        }
                        addNewPlatform()
                        count = 0
                    }

                    platforms.forEach((platform) => (platform.y -= platformSpeed));

                    const closest = platforms.find(
                        (platform) => ball.y < platform.y + 10 && ball.y > platform.y - ball.w
                    )

                    if (closest) {
                        holdAndDrop(closest)
                    } else {
                        ball.y += 5
                    }

                    ctx.clearRect(0, 0, canvasW, canvasH)

                    drawPlatforms()
                    drawBall()
                    drawScore()
                    count++

                    console.log(platforms.length)
                }, 20)
            }

            function checkGameOver() {
                if (ball.y < 0) {
                    alert("Game Over !!")
                    reset()
                }
            }

            function reset() {
                ball = { x: 150, y: 0, r: 5 }
                platforms = [{ x: 0, y: canvasH, holeX: randomHoleX(), holeW: 20 }]
                clearInterval(interval)
                clearInterval(scoreInterval)
                interval = null
                scoreInterval = null
                movePlatforms()
            }

            function addNewPlatform() {
                const lastPlatform = platforms[platforms.length - 1];
                platforms.push({
                x: 0,
                y: lastPlatform.y + platformDiff,
                holeX: randomHoleX(),
                holeW: 20,
                })
            }

            function drawPlatforms(){
                platforms.forEach((platform) => {
                    createPlatform(platform),
                    createHole(platform)
                })
            }

            function createPlatform(platform){
                    ctx.beginPath()
                    ctx.rect(platform.x, platform.y, platformW, platformH)
                    ctx.fillStyle = "blue"
                    ctx.fill()
                    ctx.closePath()
            }
            
            function createHole(platform){
                    ctx.beginPath()
                    ctx.rect(platform.holeX, platform.y, platform.holeW, platformH)
                    ctx.fillStyle = "white"
                    ctx.fill()
                    ctx.closePath()
            }

            function drawScore() {
                ctx.beginPath();
                ctx.fillStyle = "black";
                ctx.fill();
                ctx.fillText("Score: " + score, 10, 10);
                ctx.closePath();
            }

            function navigateBall() {
                document.addEventListener("keydown", (e) => {
                    if (e.getModifierState("Alt")) {
                        if (e.key === "ArrowLeft") {
                            moveSpeed = 10;
                        }

                        if (e.key === "ArrowRight") {
                            moveSpeed = 10;
                        }
                    }
                    if (e.key === "ArrowLeft") {
                        leftPressed = true;
                    }
                    if (e.key === "ArrowRight") {
                        rightPressed = true;
                    }
                });
                document.addEventListener("keyup", (e) => {
                    if (e.key === "ArrowLeft") {
                        leftPressed = false;
                    }

                    if (e.key === "ArrowRight") {
                        rightPressed = false;
                    }

                    moveSpeed = 2;
                });
            }
        </script>
    </body>
</html>