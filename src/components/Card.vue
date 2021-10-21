<template>
    <div class="Card-contain" :class="card.matched ? 'matched' : ''">
        <div class="Card-self" :class="card.flipped ? 'flipped' : ''">
            <div class="Card-face face-front"  @click="flipCard(card)">
                <i class="fa fa-question"></i>
            </div>
            <div class="Card-face face-back">
                <i :class="'fa fa-'+card.icon" :style="'color:'+card.color">
                </i>
            </div>
        </div>
    </div>
</template>

<script>
export default {
    Name: 'Card',
    props: {
        card: {
            type: Object,
            required: true,
        }
    },
    methods: {
        flipCard (card) {
            this.$emit('flip-card', card)
            }
    }
}
</script>

<style scoped>
.fa {
    font-size: 60px;
    text-align: center;
    display: flex;
    justify-content: center;
    align-items: center;
    height: 130px;
}

.matched {
    visibility: hidden;
}

.Card-contain {
  width: 100px;
  height: 130px;
  perspective: 300px;
  margin: 40px;
}

.Card-self {
  cursor: pointer;
  width: 100%;
  height: 100%;
  position: relative;
  transition: transform 1s;
  transform-style: preserve-3d;
}

.Card-face {
  position: absolute;
  height: 100%;
  width: 100%;
  backface-visibility: hidden;
}

.face-front {
  border: 1px solid #ccc;
  border-radius: 10px;
  background: #eee;
}

.face-back {
  background: #fff;
  border: 1px solid #ccc;
  transform: rotateY( 180deg );
  border-radius: 10px;
  display:block;
}

.Card-self.flipped {
  transform: rotateY(180deg);
  display:block;
}

@media all and (-ms-high-contrast: none), (-ms-high-contrast: active) { /* IE11 Fixes*/
    .Card-contain {
        perspective: none;
    }
    .Card-self {
        transition: transform 0s;
    }
    .Card-self .face-front {
        display: block;
    }
    .Card-self .face-back {
        display: none;
        transform: none;
    }
    .Card-self.flipped .face-front {
        display: none;
    }
    .Card-self.flipped .face-back {
        display: block;
        transform: none;
    }
}
</style>
