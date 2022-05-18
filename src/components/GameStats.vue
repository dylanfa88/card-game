<template>
    <div class="stats-cont">
        <div>
            <h4>Games Played</h4>
            <span>{{game_config.games_played}}</span>
        </div>
        <div>
            <h4>Wins</h4>
            <span>{{game_config.wins}}</span>
        </div>
        <div>
            <h4>Losses</h4>
            <span>{{game_config.losses}}</span>
        </div>
    </div>
</template>

<style scoped>
    .stats-cont{
        display:flex;
        padding:0 30px 30px 30px;
        margin-top:30px;
    }

    .stats-cont > div{
        border:1px solid #dadada;
        text-align: center;
        flex : 1;
        font-size:20px;
        line-height: 30px;
        padding-bottom:30px;
        border-radius: 30px;
        margin:0 30px;
        background: #fff;
    }

    .stats-cont > div > span{
        font-size:50px;
        line-height: 40px;
    }
</style>

<script>
export default {
    name: 'GameStats',
    props : ['game_config'],
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