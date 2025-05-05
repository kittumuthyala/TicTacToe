//# TicTacToe
#include <iostream>
#include <vector>
#include <limits>
using namespace std;

class TicTacToe {
public:
    TicTacToe() {
        // Initialize the board with empty spaces
        board = { {' ', ' ', ' '}, {' ', ' ', ' '}, {' ', ' ', ' '} };
        currentPlayer = 'X';  // X is the human, O is the AI
        gameOver = false;
    }

    void printBoard() {
        cout << "Current board: \n";
        for (int i = 0; i < 3; ++i) {
            for (int j = 0; j < 3; ++j) {
                cout << board[i][j];
                if (j < 2) cout << " | ";
            }
            cout << "\n";
            if (i < 2) cout << "---------\n";
        }
    }

    bool makeMove(int row, int col, char player) {
        if (row < 0 || row > 2 || col < 0 || col > 2 || board[row][col] != ' ') {
            cout << "Invalid move! Try again.\n";
            return false;
        }
        board[row][col] = player;
        return true;
    }

    void switchPlayer() {
        currentPlayer = (currentPlayer == 'X') ? 'O' : 'X';
    }

    bool checkWin() {
        // Check rows, columns, and diagonals for a winner
        for (int i = 0; i < 3; ++i) {
            if (board[i][0] == board[i][1] && board[i][1] == board[i][2] && board[i][0] != ' ')
                return true;
            if (board[0][i] == board[1][i] && board[1][i] == board[2][i] && board[0][i] != ' ')
                return true;
        }
        if (board[0][0] == board[1][1] && board[1][1] == board[2][2] && board[0][0] != ' ')
            return true;
        if (board[0][2] == board[1][1] && board[1][1] == board[2][0] && board[0][2] != ' ')
            return true;
        return false;
    }

    bool checkDraw() {
        // Check if the board is full and no winner
        for (int i = 0; i < 3; ++i) {
            for (int j = 0; j < 3; ++j) {
                if (board[i][j] == ' ') return false;
            }
        }
        return true;
    }

    int minimax(int depth, bool isMaximizingPlayer) {
        int score = evaluate();

        // If Maximizer (AI) has won, return the evaluated score
        if (score == 10) return score;

        // If Minimizer (human) has won, return the evaluated score
        if (score == -10) return score;

        // If the game is a draw, return 0
        if (checkDraw()) return 0;

        if (isMaximizingPlayer) {
            int best = -1000;
            // Traverse all cells
            for (int i = 0; i < 3; ++i) {
                for (int j = 0; j < 3; ++j) {
                    // Check if the cell is empty
                    if (board[i][j] == ' ') {
                        // Make the move and call minimax recursively
                        board[i][j] = 'O';
                        best = max(best, minimax(depth + 1, !isMaximizingPlayer));
                        // Undo the move
                        board[i][j] = ' ';
                    }
                }
            }
            return best;
        } else {
            int best = 1000;
            // Traverse all cells
            for (int i = 0; i < 3; ++i) {
                for (int j = 0; j < 3; ++j) {
                    // Check if the cell is empty
                    if (board[i][j] == ' ') {
                        // Make the move and call minimax recursively
                        board[i][j] = 'X';
                        best = min(best, minimax(depth + 1, !isMaximizingPlayer));
                        // Undo the move
                        board[i][j] = ' ';
                    }
                }
            }
            return best;
        }
    }

    int evaluate() {
        // Check rows, columns, and diagonals for a winner
        for (int row = 0; row < 3; ++row) {
            if (board[row][0] == board[row][1] && board[row][1] == board[row][2]) {
                if (board[row][0] == 'O') return 10;
                if (board[row][0] == 'X') return -10;
            }
        }

        for (int col = 0; col < 3; ++col) {
            if (board[0][col] == board[1][col] && board[1][col] == board[2][col]) {
                if (board[0][col] == 'O') return 10;
                if (board[0][col] == 'X') return -10;
            }
        }

        if (board[0][0] == board[1][1] && board[1][1] == board[2][2]) {
            if (board[0][0] == 'O') return 10;
            if (board[0][0] == 'X') return -10;
        }

        if (board[0][2] == board[1][1] && board[1][1] == board[2][0]) {
            if (board[0][2] == 'O') return 10;
            if (board[0][2] == 'X') return -10;
        }

        return 0;
    }

    void bestMove() {
        int bestVal = -1000;
        int row = -1, col = -1;

        // Try all possible moves and find the best one
        for (int i = 0; i < 3; ++i) {
            for (int j = 0; j < 3; ++j) {
                // Check if the cell is empty
                if (board[i][j] == ' ') {
                    // Make the move
                    board[i][j] = 'O';
                    int moveVal = minimax(0, false);
                    // Undo the move
                    board[i][j] = ' ';

                    // If the value of the current move is more than the best value, update best
                    if (moveVal > bestVal) {
                        row = i;
                        col = j;
                        bestVal = moveVal;
                    }
                }
            }
        }
        // Make the best move
        makeMove(row, col, 'O');
    }

    void playGame() {
        int row, col;
        while (!gameOver) {
            printBoard();
            if (currentPlayer == 'X') {
                cout << "Player X's turn. Enter row (0-2) and column (0-2): ";
                cin >> row >> col;
                if (makeMove(row, col, 'X')) {
                    if (checkWin()) {
                        printBoard();
                        cout << "Player X wins!\n";
                        gameOver = true;
                    } else if (checkDraw()) {
                        printBoard();
                        cout << "It's a draw!\n";
                        gameOver = true;
                    } else {
                        switchPlayer();
                    }
                }
            } else {
                cout << "AI (O)'s turn...\n";
                bestMove();
                if (checkWin()) {
                    printBoard();
                    cout << "AI (O) wins!\n";
                    gameOver = true;
                } else if (checkDraw()) {
                    printBoard();
                    cout << "It's a draw!\n";
                    gameOver = true;
                } else {
                    switchPlayer();
                }
            }
        }
    }

private:
    vector<vector<char>> board;
    char currentPlayer;
    bool gameOver;
};

int main() {
    TicTacToe game;
    char playAgain;

    do {
        game = TicTacToe();  // Reset the game
        game.playGame();
        cout << "Do you want to play again? (y/n): ";
        cin >> playAgain;
    } while (playAgain == 'y' || playAgain == 'Y');

    cout << "Thanks for playing!\n";
    return 0;
}
