<template>
    <div>
        <div class='gameStatus' :class='gameStatusColor'>
            {{ gameStatusMessage }}
        </div>
        <table class='grid'>
            <tr>
                <cell name='1'></cell>
                <cell name='2'></cell>
                <cell name='3'></cell>
            </tr>

            <tr>
                <cell name='4'></cell>
                <cell name='5'></cell>
                <cell name='6'></cell>
            </tr>

            <tr>
                <cell name='7'></cell>
                <cell name='8'></cell>
                <cell name='9'></cell>
            </tr>
        </table>
    
    </div>
</template>

<script>
    import cell from './Cell.vue'
    export default {
        name: 'grid',
        components: {cell},
        data () {
            return {
                //can be X or O
                activePlayer: 'O',
                //maintains Game's status turn, win, or draw
                gameStatus: 'turn',
                gameStatusMessage: `O's turn`,
                gameStatusColor: 'statusTurn',
                moves: 0,
                cells: {
                    1: '', 2: '', 3: '',
                    4: '', 5: '', 6: '',
                    7: '', 8: '', 9: ''
                },
                winConditions: [[1,2,3], [4,5,6], [7,8,9],
                                [1,4,7], [2,5,8], [3,6,9],
                                [1,5,9], [3,5,7]
                                ],

            }
        },

        methods: {
            changeGameStatus () {
                if (this.checkForWin()) {
                    return this.gameIsWon()
                } else if (this.moves === 9) {
                    return 'draw'
                }
                this.gameStatusMessage = `${this.nonActivePlayer}'s turn`
                return 'turn'
            },

            checkForWin () {
                for (let i = 0; i< this.winConditions.length; i++) {
                    let wc = this.winConditions[i]
                    let cells = this.cells
    
                    if(this.areEqual(cells[wc[0]], cells[wc[1]], cells[wc[2]])){
                        return true
                    }
                }
                return false
            },
            
            areEqual () {
                var len = arguments.length;

                for (var i = 1; i < len; i++) {
                    if(arguments[i] === '' || arguments[i] !== arguments[i-1])
                        return false
                }
                return true
            },

            gameIsWon () {
                Event.$emit('win', this.activePlayer)
                    
                this.gameStatusMessage = `${this.activePlayer} Wins !`

                Event.$emit('freeze')
                return 'win'
            },

             changePlayer () {
                this.activePlayer = this.nonActivePlayer
            }
        },

        created () {
            Event.$on('strike', (cellNumber) => {
                this.cells[cellNumber] = this.activePlayer

                this.moves++

                this.gameStatus = this.changeGameStatus()
                
                this.changePlayer()
            }),

            Event.$on('gridReset', () =>{
                Object.assign(this.$data, this.$options.data())
            })
        },
        
        computed: {
            nonActivePlayer () {
                if(this.activePlayer === 'O'){
                    return 'X'
                }
                return 'O'
            }
        },

        watch: {
            gameStatus() {
                if(this.gameStatus === 'win') {
                    this.gameStatusColor = 'statusWin'
                    return
                } else if(this.gameStatus === 'draw') {
                    this.gameStatusColor = 'statusDraw'
                    this.gameStatusMessage = 'Draw!'
                    return
                }
            }
        }
    }

</script>

<style>
    .grid {
        background-color: #34495e;
        color: #fff;
        width: 100%;
        border-collapse: collapse;
    }

    .gameStatus {
        margin: 0;
        padding: 15px;
        border-top-right-radius: 20px;
        border-top-left-radius: 20px;
        background-color: #f1c40f;
        color: #fff;
        font-size: 1.4em;
        font-weight: bold;
    }

    .statusTurn {
        background-color: #f1c40f;
    }

    .statusWin {
        background-color: #2eec71;
    }

    .statusDraw {
        background-color: #9b59b6;
    }
</style>