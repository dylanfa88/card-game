<template>
    <div class="card" :class="[card_info.suit_color, card_size]">
        <div class="card-topleft">
            <div class="card-corner-rank">{{card_info.card_value}}︎</div>
            <div class="card-corner-suit">{{card_info.card_suit}}︎</div>
        </div>
        <div class="card-bottomright">
            <div class="card-corner-rank">{{card_info.card_value}}</div>
            <div class="card-corner-suit">{{card_info.card_suit}}︎︎</div>
        </div>
        <div class="tick" v-if="show_tick">✓</div>
    </div>
</template>

<style scoped>
    .card {
        font-size:30px;
        line-height: 30px;
        display: inline-block;
        position: relative;
        width: 5em;
        height: 7em;
        background-color: #ffffff;
        box-shadow: 0 0.0625em 0.125em rgba(0, 0, 0, 0.15);
        border-radius: 0.25em;
    }
    .card.red {
        color: red;
    }
    .card.black {
        color: black;
    }
    .card-topleft {
        position: absolute;
        top: 0.5em;
        left: 0.75em;
    }
    .card-bottomright {
        position: absolute;
        bottom: 0.5em;
        right: 0.75em;
        transform: rotate(180deg);
    }
    .card-corner-rank, .card-corner-suit {
        width: 1em;
        text-align: center;
        transform: translate(-50%, 0);
    }

    .card-corner-suit{
        font-size:40px;
    }

    .card.small{
        width: 5em;
        height: 7em;
        font-size:20px;
        line-height: 20px;
        margin:0 20px;
    }

    .card.small .card-corner-suit{
        font-size:30px;
    }

    .card .tick{
        border:2px solid green;
        color:green;
        display:inline-block;
        position:absolute;
        right:-15px;
        top:-15px;
        font-size:30px;
        padding:10px 8px;
        border-radius: 50px;
    }
</style>

<script>
export default {
    name: 'SingleCard',
    props : [
        'card_info',
        'card_size',
        'show_tick'
    ],
    data : function()
    {
        return {
        }
    },
    created : function()
    {

    }
}
</script>