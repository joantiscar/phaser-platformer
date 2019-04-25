<template>
  <div id="game"></div>
</template>
<script>
import Phaser from 'phaser'
import wall from '../assets/wall.png'
import ground from '../assets/ground.png'
import player from '../assets/player.png'

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
            y: 100
          }
        }
      },
      // NO HI HA STATES A 3.0 -> SCENES
      scene: {
        preload () {
          console.log('PRELOAD')
          this.load.image('wall', wall)
          this.load.image('ground', ground)
          this.load.spritesheet('player', player, { frameWidth: 28, frameHeight: 22 })
          // CARREGAR ASSETS -> IMAAGES, PLAYERS (SPRITESHEETS), AUDIOS

          // AUDIO
          // this.load.setBaseURL('http://labs.phaser.io')
          this.dustSound = this.load.audio('dust', ['../assets/dust.wav', '../assets/dust.mp3'])
        },
        create () {
          console.log('CREATE')
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

          // cursors

          this.cursors = this.input.keyboard.createCursorKeys()
        },
        update () {
          // ESTE EL QUE S'executa continuament al Game loop -> 60 vegades per segon o FPS

          // INPUT EVENTS
          if (this.cursors.left.isDown) {
            this.player.setVelocityX(-160)
            this.player.setFrame(2)
          } else if (this.cursors.right.isDown) {
            this.player.setVelocityX(160)
            this.player.setFrame(1)
          } else {
            if (this.player.body.velocity.x > 0) this.player.setVelocityX(this.player.body.velocity.x - 1)
            if (this.player.body.velocity.x < 0) this.player.setVelocityX(this.player.body.velocity.x + 1)

            this.player.setFrame(0)
          }
          if (this.player.body.touching.down && this.cursors.up.isDown) {
            this.player.setVelocityY(200)
          }

          if (this.player.body.touching.down && this.player.y > 10) {
            // this.dustSound.play()
          }
        }
      }
    }
    // eslint-disable-next-line
    new Phaser.Game(config)
  },
  methods: {}
}
</script>
<style scoped></style>
