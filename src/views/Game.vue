<template>
  <div id="game"></div>
</template>
<script>
import Phaser from 'phaser'
import wall from '../assets/wall.png'
import coin from '../assets/coin.png'
import ground from '../assets/ground.png'
import player from '../assets/player.png'
import dust from '../assets/dust.wav'
import jump from '../assets/jump.wav'

function takeCoin (player, coin) {
  coin.disableBody(true, true)
}

function die (player, coin) {
  coin.disableBody(true, true)
}

function jumpPlayer (game) {
  if (game.player.body.touching.down && game.player.body.velocity.y < 100) {
    game.hasJumped = true
    // game.sound.play('jump')
    game.player.body.velocity.y = -220
  }
}

export default {
  name: 'Game',
  mounted () {
    // PHASER 2.6 -> phaser-ce
    // Phaser 3.0 -> phaser

    let config = {
      type: Phaser.AUTO,
      width: 500,
      height: 200,
      physics: {
        default: 'arcade',
        arcade: {
          gravity: {
            y: 600
          }
        }
      },
      // NO HI HA STATES A 3.0 -> SCENES
      scene: {
        preload () {
          console.log('PRELOAD')
          this.load.image('wall', wall)
          this.load.image('ground', ground)
          this.load.image('coin', coin)
          this.load.spritesheet('player', player, { frameWidth: 28, frameHeight: 22 })
          // CARREGAR ASSETS -> IMAAGES, PLAYERS (SPRITESHEETS), AUDIOS

          // AUDIO
          // this.load.setBaseURL('http://labs.phaser.io')
          this.dustSound = this.load.audio('dust', dust)
          this.jumpSound = this.load.audio('jump', jump)
        },
        create () {
          console.log('CREATE')

          this.debugSpeedY = this.add.text(16, 28, 'Y: ', { fontSize: '12px', fill: '#000' })
          this.debugHasJumped = this.add.text(16, 16, 'jumped: ', { fontSize: '12px', fill: '#000' })
          this.debugJumping = this.add.text(16, 0 , 'jumping: ', { fontSize: '12px', fill: '#000' })
          // INITIALIZE del nivell -> Afegirem tiles (level: pareds, terres), afegirem players, collectibles (coins)
          this.cameras.main.backgroundColor.setTo(69, 911, 420)

          // ROUP PER DEFECTE ES DINAMIC

          this.level = this.physics.add.staticGroup()
          this.level.create(this.game.config.width / 2, this.game.config.height / 2 + 30, 'ground')
          this.level.create(this.game.config.width / 2 + 160, this.game.config.height / 2, 'wall')
          this.level.create(this.game.config.width / 2 - 160, this.game.config.height / 2, 'wall')
          // Player -> FÍSIQUES
          this.player = this.physics.add.sprite(this.game.config.width / 2, this.game.config.height / 2 - 50, 'player')
          this.player.setCollideWorldBounds(true)
          this.player.setBounce(0.2)
          this.physics.add.collider(this.player, this.level)
          // this.add.audio(this.dustSound, 0.1)

          // ADD COINS
          this.coins = this.physics.add.group()

          this.coins.create(140, 200 / 2, 'coin')
          this.coins.create(170, 200 / 2, 'coin')
          this.coins.create(200, 200 / 2, 'coin')

          this.physics.add.collider(this.coins, this.level)
          this.physics.add.overlap(this.coins, this.player, takeCoin, null, this)
          // cursors

          this.cursors = this.input.keyboard.createCursorKeys()

          // ANIMATE PLAYER
          this.anims.create({
            key: 'idle',
            frames: this.anims.generateFrameNumbers('player', { start: 3, end: 5 }),
            frameRate: 5,
            repeat: -1
          })
        },
        update () {
          this.debugSpeedY.setText('y: ' + this.player.body.velocity.y)
          this.debugHasJumped.setText('jumped: ' + this.hasJumped)
          this.debugJumping.setText('jumping: ' + this.jumping)

          this.player.anims.play('idle', true)
          // ESTE EL QUE S'executa continuament al Game loop -> 60 vegades per segon o FPS

          // INPUT EVENTS
          if (this.cursors.left.isDown) {
            this.player.setVelocityX(-160)
            this.player.setFrame(2)
          } else if (this.cursors.right.isDown) {
            this.player.setVelocityX(160)
            this.player.setFrame(1)
          } else {
            if (this.player.body.velocity.x > 0) this.player.setVelocityX(this.player.body.velocity.x - 10)
            if (this.player.body.velocity.x < 0) this.player.setVelocityX(this.player.body.velocity.x + 10)
            if (this.player.body.velocity.x < 10 && this.player.body.velocity.x > -10) this.player.setVelocityX(0)

            this.player.setFrame(0)
          }
          if (this.cursors.up.isDown) {
            jumpPlayer(this)
          }
          if (this.player.body.onFloor() && this.player.body.velocity.y ) {
            if (this.hasJumped) {
              console.log('astio')
              this.sound.play('dust')
              this.hasJumped = false
            }
          }
        }

      }

    }
    // eslint-disable-next-line
    this.game = new Phaser.Game(config)
  },
  methods: {}
}
</script>
<style scoped></style>
