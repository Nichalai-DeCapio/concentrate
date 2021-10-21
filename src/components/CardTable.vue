<template>
    <ul class="Card-Table">
        <li :key="card.index" 
            v-for="card in cards" @click="$emit('flip-card', card)"
        >
            <Card :card="card" />
        </li>
    </ul>


</template>
<script>
import Card from './Card'

export default {
    name: 'CardTable',
    components: {
        Card
    },
    props: {
        cards: {
            type: Array,
            required: true,
        }
    }
}
</script>
<style scoped>

.Card-Table {
    list-style-type: none;
    display: flex;
    flex-wrap: wrap;
    justify-content: space-evenly;
    width: 90%;
    margin: 0 auto;
}

@media all and (-ms-high-contrast: none), (-ms-high-contrast: active) { /* IE11 Fix */
    .Card-Table {
        justify-content: space-around;
    }
}

</style>
