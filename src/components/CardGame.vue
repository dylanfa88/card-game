<template>
    <div id="card-game-cont">
        <h1>Card Game</h1>
        <div class="game-area">
            <div v-if="current_game_mode == 1">
                <section class="start-game-section">
                    <a href="#" class="special-eff-green" v-on:click.prevent="start_new_game">Start Game!</a>
                    <game-stats class="mt-5" :game_config="game_config"></game-stats>
                </section>
            </div>

            <div v-else-if="current_game_mode == 2">
                <section class="game-section">
                    <div class="game-section-1">
                        <h2 class="mb-5">Your Current Card</h2>
                        <single-card class="mt-5" :card_info="old_card" :card_size="'big'" :show_tick="0"></single-card>
                    </div>
                    <div class="game-section-2">
                        <h2 class="mb-10">Will the next card be<br>Higher or Lower?</h2>
                        <div class="guess-card-cont mt-5">
                            <a href="#" class="special-eff-red" v-on:click.prevent="guess_card(0)">
                                <span>↓</span>
                                Lower
                            </a>
                            <a href="#" class="special-eff-green" v-on:click.prevent="guess_card(1)">
                                <span>↑</span>
                                Higher
                            </a>
                        </div>
                    </div>
                    <div class="game-section-3">
                        <h2 class="mb-5">Your Score : {{game_config.current_score}}/{{game_config.win_at}}</h2>
                        <div class="mt-5">
                            <div class="playing-deck">
                                <single-card class="mt-5" v-for="card in playing_deck" :card_info="card" v-bind:key="card.id" :card_size="'small'" :show_tick="1"></single-card>
                            </div>
                        </div>
                    </div>
                </section>
            </div>

            <div v-else-if="current_game_mode == 3">
                <section class="game-results">
                    <div class="result-win" v-if="game_config.last_result == 'won'">
                        <h2>Congratulations you won!</h2>
                        <div class="text-center mt-5">
                            <div class="playing-deck">
                                <single-card class="mt-5" v-for="card in playing_deck" :card_info="card" v-bind:key="card.id" :card_size="'small'" :show_tick="1"></single-card>
                            </div>
                        </div>
                    </div>
                    <div class="result-lost" v-else>
                        <h2>Opps! Seems like you got the wrong answer!</h2>
                        <div class="text-center mt-5 wrong-deck">
                            <div>
                                <span>You Old Card</span>
                                <single-card class="mt-5" :card_info="old_card" :card_size="'small'" :show_tick="0"></single-card>
                            </div>
                            <div class="wrong-deck-text">
                                You choice of <strong>{{ (new_card.decision == 1) ? 'Higher' : 'Lower'  }}</strong> was incorret!
                            </div>
                            <div>
                                <span>You New Card</span>
                                <single-card class="mt-5" :card_info="new_card" :card_size="'small'" :show_tick="0"></single-card>
                            </div>
                        </div>
                    </div>
                    <div class="result-action">
                        <a href="#" class="special-eff-green" v-on:click.prevent="start_new_game">Start New Game!</a>
                    </div>
                    <game-stats :game_config="game_config"></game-stats>
                    <div class="text-center">
                        <a href="#" class="clear-scores" v-on:click.prevent="clear_metrics">Clear Scores</a>
                    </div>
                </section>
            </div>
        </div>

        <div class="info-div" v-if="show_info_div">
            <pre>OLD CARD {{old_card}}</pre>
            <pre>NEW CARD {{new_card}}</pre>
            <pre>Current Score : {{game_config.current_score}}</pre>
            <pre>Games Played : {{game_config.games_played}}</pre>
            <pre>Wins : {{game_config.wins}}</pre>
            <pre>Losses : {{game_config.losses}}</pre>
            <pre>Last Result : {{game_config.last_result}}</pre>
            <hr>
            <pre>current_deck.length : {{current_deck.length}}</pre>
            <pre style="height:500px;overflow: auto;">{{current_deck}}</pre>
        </div>

        <span class="sub-text">By Dylan Fernandes</span>
    </div>
</template>

<style scoped>

</style>

<script>
    import SingleCard from './SingleCard.vue';
    import GameStats from './GameStats.vue';

    export default {
        name: 'CardGame',
        components : {
            SingleCard,
            GameStats
        },
        data : function()
        {
            return {
                game_config : {
                    current_score : 0,
                    win_at : 5,
                    games_played : 0,
                    wins : 0,
                    losses : 0,
                    last_result : ''
                },
                current_game_mode : 1,
                current_deck : [],
                playing_deck : [],
                old_card : {},
                new_card : {},
                show_info_div : 0
            }
        },
        mounted() {
            if(localStorage.games_played)
            {
                this.game_config.games_played = localStorage.games_played;
                this.game_config.wins         = localStorage.wins;
                this.game_config.losses       = localStorage.losses;
            }
            else
            {
                localStorage.games_played = 0;
                localStorage.wins         = 0;
                localStorage.losses       = 0;
            }
        },
        methods : {
            start_new_game : function()
            {
                //reset the configs
                this.game_config.current_score = 0;
                this.game_config.last_result = '';

                //0. change game mode
                this.current_game_mode = 2;

                //1. Flush the deck
                this.current_deck = [];
                this.playing_deck = [];

                //2. Create a new deck
                this.current_deck = this.create_deck();

                //3. Shuffle the deck
                this.shuffle_deck();

                this.new_card = {};
                this.old_card = this.draw_card();
            },
            create_deck : function()
            {
                let deck = [];
                const suits = ['♠', '♥', '♣', '♦'];
                const face_cards = ['J', 'Q', 'K', 'A'];
                suits.map(suit => {
                    //determine the color
                    let suit_color = (suit == '♠' || suit == '♣') ? 'black' : 'red';

                    //add the number cards
                    for(let i = 2; i <= 10; i++)
                    {
                        deck.push({id : this.generate_random_id(), power : i, card_value : i, card_suit : suit, suit_color : suit_color, hidden : 1, decision : ''});
                    }
                    //add the face cards
                    let face_power = 10;
                    face_cards.map(face_card => {
                        face_power++;
                        deck.push({id : this.generate_random_id(), power : face_power, card_value : face_card, card_suit : suit, suit_color : suit_color, hidden : 1, decision : ''})
                    });
                });
                return deck;
            },
            shuffle_deck : function()
            {
                //shuffle the deck
                if(this.current_deck.length > 0)
                {
                    for (let i = this.current_deck.length - 1; i > 0; i--) {
                        const j = Math.floor(Math.random() * (i + 1));
                        [this.current_deck[i], this.current_deck[j]] = [this.current_deck[j], this.current_deck[i]];
                    }
                }
            },
            generate_random_id : function()
            {
                return (Math.random() + 1).toString(36).substring(7);
            },
            draw_card : function()
            {
                //randomly draw a card from the current deck
                return this.current_deck.splice(Math.floor(Math.random()*this.current_deck.length), 1)[0];
            },
            guess_card : function(decision)
            {
                //1. draw a new card
                this.new_card = this.draw_card();
                this.new_card.decision = decision;

                //2. check if the user is right
                if(decision == 1)
                {
                    (this.old_card.power < this.new_card.power) ? this.right_guess() : this.wrong_guess();
                }
                else
                {
                    (this.old_card.power > this.new_card.power) ? this.right_guess() : this.wrong_guess();
                }
            },
            right_guess : function()
            {
                //update the score
                this.game_config.current_score++;

                //update the cards
                this.playing_deck.push(this.new_card);
                this.old_card = this.new_card;

                //check if user has won
                if(this.game_config.current_score >= this.game_config.win_at)
                {
                    //update games played
                    this.update_metrics('play');

                    this.update_metrics('win');
                    this.game_config.last_result = 'won';
                    this.current_game_mode = 3;
                }
            },
            wrong_guess : function()
            {
                //update games played
                this.update_metrics('play');

                //mark the loss
                this.update_metrics('loss');
                this.game_config.last_result = 'lost';
                this.current_game_mode = 3;
            },
            update_metrics : function(result)
            {
                //move to database for more persistance
                if(result == 'play')
                {
                    this.game_config.games_played++;
                    localStorage.games_played++;
                }
                else if(result == 'win')
                {
                    this.game_config.wins++;
                    localStorage.wins++;
                }
                else if(result == 'loss')
                {
                    this.game_config.losses++;
                    localStorage.losses++;
                }
            },
            clear_metrics : function()
            {
                this.game_config.games_played = 0;
                this.game_config.wins         = 0;
                this.game_config.losses       = 0;

                localStorage.games_played = 0;
                localStorage.wins         = 0;
                localStorage.losses       = 0;
            }
        }
    }
</script>