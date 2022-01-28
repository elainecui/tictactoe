	import java.util.*;
	
	public class TicTacToe{
		static String[] board = new String[9]; // array for board
		static int count1 = 0; // variable to count player 1's total incorrect entries
		static int count2 = 0; // variable to count player 2's total incorrect entries
		static int consecutive1 = 0; // variable to count player 1's consecutive incorrect entries
		static int consecutive2 = 0; // variable to count player 2's consecutive incorrect entries
		static int help1 = 0; // variable to count player 1's CPU assist
		static int help2 = 0; // variable to count player 2's CPU assist
		static int p1LastMove = 0; // variable to keep track of player 1's last move
		static int p2LastMove = 0; // variable to keep track of player 2's last move
		static String winner = null; // variable to keep track of the winner
		static boolean forfeit = false; 
		static int cpuRound = 1; // variable to count the number of CPU's moves
		static int[] cornerSpaces = {0, 2, 6, 8};
	
	public static void main(String[] args){
		fillEmptyBoard(); // call fillEmptyBoard() method
		Scanner input = new Scanner(System.in); // create a Scanner object
		System.out.print("Enter 1 for 2 players mode or enter 2 for playing against the CPU or enter 0 to quit: "); // prompt user
		int mode = input.nextInt(); // read input as an Int
		if (mode == 1) { // if 2 player mode is chosen
			printBoard(); // call printBoard() method
			while (winner == null) { // while there is no winner
				player1Turn(); // call player1Turn() method
				if (forfeit) { // if player 1 forfeits, exit game
					System.exit(0);
				}
				winner = checkWinner(); // check for winner
				if (winner != null) // if there is a winner, break 
					break;
				player2Turn(); // call player2Turn method()
				if (forfeit) { // if player 2 forfeits, exit game
					System.exit(0);
				}
				winner = checkWinner(); // check for winner
			}

			if (winner == "1"){ // if player 1 wins
				System.out.println("Game Over! Player 1 Wins!");
				System.exit(0);
			}
			else if (winner == "2"){ // if player 2 wins
				System.out.println("Game Over! Player 2 Wins!");
				System.exit(0);
			}
			else if (winner == "tie"){ // if there is a tie
				System.out.println("Game Over! It's a Tie!");
				System.exit(0);
			}
		}

		else if (mode == 2) { // if player vs CPU mode is chosen
			printBoard(); // call printBoard() method
			while (winner == null){ // while there is no winner
				player1Turn(); // player's turn
				if (forfeit){ // if player forfeits, exit
					System.exit(0);
				}
				winner = checkWinner(); // check for winner
				if (winner != null) // if there is a winner, break
					break;
				cpuTurn(); // cpu's turn
				cpuRound += 1; // add to cpuRound
				winner = checkWinner(); // check for winner
			}

			if (winner == "1"){ // if player 1 wins
				System.out.println("Game Over! Player 1 Wins!");
				System.exit(0);
			}
			else if (winner == "2"){ // if cpu wins
				System.out.println("Game Over! CPU Wins!");
				System.exit(0);
			}
			else if (winner == "tie"){ // if there is a tie
				System.out.println("Game Over! It's a Tie!");
				System.exit(0);
			}
		}
	}

	public static void printBoard() { // method for printing board
		System.out.println();
		System.out.println(" " + board[0] + " | " + board[1] + " | " + board[2] + " ");
		System.out.println("---+---+---");
		System.out.println(" " + board[3] + " | " + board[4] + " | " + board[5] + " ");
		System.out.println("---+---+---");
		System.out.println(" " + board[6] + " | " + board[7] + " | " + board[8] + " ");
		System.out.println();
	}

	public static void fillEmptyBoard(){ // method for filling board with numbers 1 - 9
		for (int i = 0; i < 9; i++) {
			board[i] = String.valueOf(i + 1);
		}
	}

	public static void player1Turn() { //method for player 1 in 2 player mode
		Scanner input = new Scanner(System.in); // create a Scanner object
		System.out.print("Player 1 turn: "); // prompt player 1
		String user = input.nextLine(); // read player 1's input as a String
		int p1 = 0; // variable for player 1's chosen board number

		//check if player 1 entered a valid number
		if (user.equals("1") || user.equals("2") || user.equals("3") || user.equals("4") || user.equals("5") || user.equals("6") || user.equals("7") || user.equals("8") || user.equals("9")) {
			int turn1 = Integer.parseInt(user); // convert user String input into an integer
			//reset consecutive incorrect entries to 0
			if (consecutive1 != 0) {
				consecutive1 = 0;
			}
			p1 = turn1 - 1; // update p1

			//check if player 1 entered a number for a space already taken
			if (board[p1] == "X" || board[p1] == "O"){
				count1 += 1; // accumulate incorrect entry count
				consecutive1 += 1; // accumulate consecutive incorrect entry count
				if (count1 == 5 || consecutive1 == 3) { // if player 1 has 5 total incorrent entries or 3 consecutive incorrect entries
					System.out.println("Player 1 forfeits the game due to reaching maximum incorrect entries"); // error message
					forfeit =  true; // set forfeit as true
					System.exit(0); // exit gam
				}
				System.out.println("Incorrect entry, please try again"); // error message 
				player1Turn(); // prompt player 1 again
			}

			//make the move if space is not yet taken
			else if (board[p1] != "O" && board[p1] != "X"){
				board[p1] = "X"; // mark board with X
				p1LastMove = p1; // update p1LastMove
				printBoard(); // print board
			}
		}

		else if (user.equals("0")) { // if player 1 chooses to forfeit
			System.out.println("Player 1 forfeits the game");
			forfeit =  true;
			System.exit(0);
		}

		else if (user.equals("000")){ // if player 1 asks for CPU assist
			if (help1 >= 2){ // if player 1 has already used up 2 CPU assists
				System.out.println("Player 1 reaches max CPU assists, please try again"); // error message
				player1Turn(); // prompt player 1 again
			}
			else if (help1 < 2){ // if player 1 has used less than 2 CPU assists
				p1LastMove = cpuHelp(1, p1LastMove); // call cpuHelp() method
				help1 += 1; // add 1 to help1
				consecutive1 = 0; // reset consecutive1
			}
		}

	//player makes an invalid entry
  	else {
			count1 += 1; // add 1 to count1
			consecutive1 += 1; // add1 to consecutive1
			if (count1 == 5 || consecutive1 == 3) { // if player 1 has 5 total incorrent entries or 3 consecutive incorrect entries
				System.out.println("Player 1 forfeits the game due to reaching maximum incorrect entries"); // error message
				forfeit =  true;
				System.exit(0);
			}
			System.out.println("Incorrect entry, please try again"); // error message
			player1Turn(); // prompt player 1 again
		}
	}

	public static void player2Turn() { // method for player 2 in 2 player mode, same logic as player1Turn() method
		Scanner input = new Scanner(System.in);
		System.out.print("Player 2 turn: ");
		String user = input.nextLine();
		int p2 = 0;
		
		if (user.equals("1") || user.equals("2") || user.equals("3") || user.equals("4") || user.equals("5") || user.equals("6") || user.equals("7") || user.equals("8") || user.equals("9")) {
			int turn2 = Integer.parseInt(user);
			//reset consecutive incorrect entries to 0
			if (consecutive2 != 0) {
				consecutive2 = 0;
			}
			p2 = turn2 - 1;

			if (board[p2] == "X" || board[p2] == "O"){
				count2 += 1;
				consecutive2 += 1;
				if (count2 == 5 || consecutive2 == 3) {
					System.out.println("Player 2 forfeits the game due to reaching maximum incorrect entries");
					forfeit = true;
					System.exit(0);
				}
				System.out.println("Incorrect entry, please try again");
				player2Turn();
			}

			else if (board[p2] != "X" && board[p2] != "O"){
				board[p2] = "O";
				p2LastMove = p2;
				printBoard();
			}
		}
		else if (user.equals("0")) {
			System.out.println("Player 2 forfeits the game");
			forfeit = true;
			System.exit(0);
		}
		else if (user.equals("000")){
			if (help2 >= 2){
				System.out.println("Player 2 reaches max CPU assists, please try again");
				player2Turn();
			}
			else if (help2 < 2){
				p2LastMove = cpuHelp(2, p2LastMove);
				help2 += 1;
				consecutive2 = 0;
			}
		}
		else {
			count2 += 1;
			consecutive2 += 1;
			if (count2 == 5 || consecutive2 == 3) {
				System.out.println("Player 2 forfeits the game due to reaching maximum incorrect entries");
				forfeit = true;
				System.exit(0);
			}
			System.out.println("Incorrect entry, please try again");
			player2Turn();
		}
	}
		
	public static void playerTurn() { // method for player in CPU vs player mode, same logic as player1Turn() method
		Scanner input = new Scanner(System.in);
		System.out.print("Player 1 turn: ");
		String user = input.nextLine();
		int turn = 0;
		if (user.equals("1") || user.equals("2") || user.equals("3") || user.equals("4") || user.equals("5") || user.equals("6") || user.equals("7") || user.equals("8") || user.equals("9")) {
			int p = Integer.parseInt(user);
			turn = p - 1;
			board[turn] = "X";
		}
		else if (user.equals("0")) {
			System.out.println("Player forfeits the game");
			forfeit = true;
		}
		else {
			count1 += 1;
			consecutive1 += 1;
			if (count1 == 5 || consecutive1 == 3) {
				System.out.println("Player forfeits the game due to reaching maximum incorrect entries");
				forfeit =  true;
				System.exit(0);
			}
			System.out.println("Incorrect entry, please try again");
			playerTurn();
		}
	}

	public static void cpuTurn(){ // method for CPU's turn to play
		String[] remainingSpaces = findRemainingSpaces(); // check remaining spaces on board
		int cpuLastMove = 0;
		String[] nextTo = new String[2]; 
		String[] cpuWinCheck = new String[2];
		String[] cpuAttack = new String[2];
		nextTo = checkNextTo("X"); // call checkNextTo method
		cpuWinCheck = cpuWin("O"); // call cpuWin method
		cpuAttack = cpuAttack(cpuLastMove); // call cpuAttack method
		
		
		if (board[4].equals("5")){ // if center tile is not taken, take it
			board[4] = "O";
			cpuLastMove = 4; // update cpuLastMove
		}

		else if (cpuRound == 1 && board[4].equals("X")){ // if player chooses center tile in first turn, choose a random corner space
			int random = (int) (Math.random() * 4);
			int space = cornerSpaces[random]; 
			board[space] = "O"; // CPU fills random corner space
			cpuLastMove = space; // update cpuLastMove
		}

		else if (cpuWinCheck[0] == "true"){ // if there is a chance for CPU to win, take the remaining tile
			for (int i = 0; i < 9; i++){
				if (board[i].equals(cpuWinCheck[1])){ // if space is not taken
					board[i] = "O"; // CPU fills remaining space in that row / column / diagonal
					cpuLastMove = i; // update cpuLastMove
				}
			} 
		}

		else if (nextTo[0].equals("true")){ // if player 1 places 2 Xs in the same row / column / diagonal
			for (int i = 0; i < 9; i++){
				if (board[i].equals(nextTo[1])){ // if space is not taken
					board[i] = "O"; // CPU fills remaining space in that row / column / diagonal
					cpuLastMove = i; // update cpuLastMove
				}
			}
		}

		else if (cpuAttack[0].equals("true")){ // if CPU doesn't have to win or defend, mark a tile next to what it previously marked
			for (int i = 0; i < 9; i++){
				if (board[i].equals(cpuAttack[1])){ // if space is not taken
					board[i] = "O"; // CPU fills remaining space in that row / column / diagonal
					cpuLastMove = i; // update cpuLastMove
				}
			}
		}

		else{ // if none of the above cases work, choose a random remaining space on the board
			int random = (int) (Math.random() * (remainingSpaces.length));
			for (int i = 0; i < board.length; i++){
				if (board[i].equals(remainingSpaces[random])){
					board[i] = "O";
					cpuLastMove = i; // update cpuLastMove
				}
			}
		}
		System.out.println("CPU's turn: ");
		printBoard(); // print board
	}

	public static int cpuHelp(int i, int j){ // first argument is player number, second argument is player's last turn 
		String[] remainingSpace = findRemainingSpaces(); // check remaining spaces on board
		String[] nextTo = new String[2]; 
		String[] cpuWinCheck = new String[2];  
		String[] cpuAttack = new String[2];
		String player = "";
		String opponent = "";
		int playerLastMove = 0;
		if (i == 1){ // if player 1 calls cpuHelp() method, set the following variables
			player = "X";
			opponent = "O";
			nextTo = checkNextTo("O");
			cpuWinCheck = cpuWin("X");
			cpuAttack = cpuAttack(p1LastMove);
		}
		else if (i == 2){ // if player 2 calls cpuHelp() method, set the following variables
			player = "O";
			opponent = "X";
			nextTo = checkNextTo("X");
			cpuWinCheck = cpuWin("O");
			cpuAttack = cpuAttack(p2LastMove);
		}

		if (board[4].equals("5")){ // if center tile is not taken, take the center tile
			board[4] = player;
			playerLastMove = 4; // update playerLastMove
		}

		else if (cpuWinCheck[0] == "true"){ // if the player can win, take the winning tile
			for (int k = 0; k < 9; k++){
				if (board[k].equals(cpuWinCheck[1])){ // if space is not taken
					board[k] = player; // CPU fills remaining space in that row / column / diagonal
					playerLastMove = k; // update playerLastMove
				}
			} 
		}

		else if (nextTo[0].equals("true")){ // if opponent player places 2 opponent markers in the same row / column / diagonal
			for (int k = 0; k < 9; k++){
				if (board[k].equals(nextTo[1])){ // if space is not taken
					board[k] = player; // CPU fills remaining space in that row / column / diagonal
					playerLastMove = k; // update playerLastMove
				}
			}
		}

		else if (cpuAttack[0].equals("true")){ // if player doesn't need to win or defend, mark the tile next to what they previously played
			for (int k = 0; k < 9; k++){
				if (board[k].equals(cpuAttack[1])){ // if space is not taken
					board[k] = player; // CPU fills remaining space in that row / column / diagonal
					playerLastMove = k; // update playerLastMove
				}
			}
		}

		else{ // otherwise if none of those situations occur
			int random = (int) (Math.random() * (remainingSpace.length));
			for (int k = 0; k < board.length; k++){
				if (board[k].equals(remainingSpace[random])){ // if space is not taken
					board[k] = player; // CPU fills space
					playerLastMove = k; // update playerLastMove
				}
			}
		}
		printBoard(); // print board
		return playerLastMove;
	}	

	public static String checkWinner(){ // method for checking to see if there is a winner
        for (int i = 0; i < 8; i++){
            String line = null; 
            switch (i) { // add up values in that row/column/diagonal depending on i value
                case 0: line = board[0] + board[1] + board[2]; break;
                case 1: line = board[3] + board[4] + board[5]; break;
                case 2: line = board[6] + board[7] + board[8]; break;
                case 3: line = board[0] + board[3] + board[6]; break;
                case 4: line = board[1] + board[4] + board[7]; break;
                case 5: line = board[2] + board[5] + board[8]; break;
                case 6: line = board[0] + board[4] + board[8]; break;
                case 7: line = board[2] + board[4] + board[6]; break;
            }

            if (line.equals("XXX")) // if player 1 wins
            	return "1";
            else if (line.equals("OOO")) // if player 2 / CPU wins
            	return "2";
        }

        String boardS = "";
        for (int i = 0; i < 9; i++){
        	boardS = String.valueOf(i + 1);
        	if (Arrays.asList(board).contains(boardS)){ // if not all the tiles are filled
        		break;
        	}
        	else if (i == 8) // if all the tiles are filled
        		return "tie"; // return tie
        }
        return null; // return null if no winner
    }

    public static int checkSpacesLeft(){ // method for counting the number of spaces left
    	int spacesLeft = 0;
		for (int i = 0; i < board.length; i++) {
			if (board[i] != "X" && board[i] != "O") { // if tiles is not marked with "X" or "O"
				spacesLeft += 1;
			}
		}
		return spacesLeft;
    }

    public static String[] findRemainingSpaces(){ // method for finding the tiles that are remaining
    	int spacesLeft = checkSpacesLeft(); // call checkSpacesLeft() method
    	String[] remainingSpaces = new String[spacesLeft];
		for (int i = 0; i < board.length; i++) {
			int j = 0;
			if (board[i] != "X" && board[i] != "O") { // if tiles is not marked with "X" or "O"
				remainingSpaces[j] = board[i]; // change array element to that tile
				j += 1;
			}
		}
		return remainingSpaces;
    }

    public static String[] checkNextTo(String s){ // method for CPU to check if player placed 2 Xs in the same row/column/diagonal
    	String[] nextTo = {"true", "0"}; // first element is boolean for whether there is 2 Xs is the same row/column/diagonal, second element is where CPU should place
    	if ((board[0] == s && board[1] == s) && (board[2] != "X" && board[2] != "O")) // checking in first row
    		nextTo[1] = "3";
    	else if ((board[1] == s && board[2] == s) && (board[0] != "X" && board[0] != "O"))
    		nextTo[1] = "1";
    	else if ((board[0] == s && board[2] == s) && (board[1] != "X" && board[1] != "O"))
    		nextTo[1] = "2";
    	else if ((board[3] == s && board[4] == s) && (board[5] != "X" && board[5] != "O"))// checking in second row
    		nextTo[1] = "6";
    	else if ((board[4] == s && board[5] == s) && (board[3] != "X" && board[3] != "O"))
    		nextTo[1] = "4"; 
    	else if ((board[3] == s && board[5] == s) && (board[4] != "X" && board[4] != "O"))
    		nextTo[1] = "5";
    	else if ((board[6] == s && board[7] == s) && (board[8] != "X" && board[8] != "O"))// checking in third row
    		nextTo[1] = "9";
    	else if ((board[7] == s && board[8] == s) && (board[6] != "X" && board[6] != "O"))
    		nextTo[1] = "7";
    	else if ((board[6] == s && board[8] == s) && (board[7] != "X" && board[7] != "O"))
    		nextTo[1] = "8";
    	else if ((board[0] == "X" && board[3] == s) && (board[6] != "X" && board[6] != "O"))// checking in first column
    		nextTo[1] = "7";
    	else if ((board[3] == s && board[6] == s) && (board[0] != "X" && board[0] != "O"))
    		nextTo[1] = "1";
    	else if ((board[0] == s && board[6] == s) && (board[3] != "X" && board[3] != "O"))
    		nextTo[1] = "4";
    	else if ((board[1] == s && board[4] == s) && (board[7] != "X" && board[7] != "O"))// checking in second column
    		nextTo[1] = "8";
    	else if ((board[4] == s && board[7] == s) && (board[1] != "X" && board[1] != "O"))
    		nextTo[1] = "2";
    	else if ((board[1] == s && board[7] == s) && (board[4] != "X" && board[4] != "O"))
    		nextTo[1] = "5";
    	else if ((board[2] == s && board[5] == s) && (board[8] != "X" && board[8] != "O"))// check in third column
    		nextTo[1] = "9";
    	else if ((board[5] == s && board[8] == s) && (board[2] != "X" && board[2] != "O"))
    		nextTo[1] = "3";
    	else if ((board[2] == s && board[8] == s) && (board[5] != "X" && board[5] != "O"))
    		nextTo[1] = "6";
    	else if ((board[2] == s && board[4] == s) && (board[6] != "X" && board[6] != "O"))// checking in left diagonal
    		nextTo[1] = "7";
    	else if ((board[4] == s && board[6] == s) && (board[2] != "X" && board[2] != "O"))
    		nextTo[1] = "3";
    	else if ((board[2] == s && board[6] == s) && (board[4] != "X" && board[4] != "O"))
    		nextTo[1] = "5";
    	else if ((board[0] == s && board[4] == s) && (board[8] != "X" && board[8] != "O"))// checking in right diagonal
    		nextTo[1] = "9";
    	else if ((board[4] == s && board[8] == s) && (board[0] != "X" && board[0] != "O"))
    		nextTo[1] = "1";
    	else if ((board[0] == s && board[8] == s) && (board[4] != "X" && board[4] != "O"))
    		nextTo[1] = "5";
    	else
    		nextTo[0] = "false"; // otherwise, change element in index 0 to "false"

    	return nextTo;
    }

    public static String[] cpuWin(String s){ // method for CPU to check if it can win
    	String[] check = {"true", "0"}; // first element is boolean for whether there is 2 Os is the same row/column/diagonal, second element is where CPU should place
    	if ((board[0] == s && board[1] == s) && (board[2] != "X" && board[2] != "O")) // checking in first row
    		check[1] = "3";
    	else if ((board[1] == s && board[2] == s) && (board[0] != "X" && board[0] != "O"))
    		check[1] = "1";
    	else if ((board[0] == s && board[2] == s) && (board[1] != "X" && board[1] != "O"))
    		check[1] = "2";
    	else if ((board[3] == s && board[4] == s) && (board[5] != "X" && board[5] != "O"))// checking in second row
    		check[1] = "6";
    	else if ((board[4] == s && board[5] == s) && (board[3] != "X" && board[3] != "O"))
    		check[1] = "4"; 
    	else if ((board[3] == s && board[5] == s) && (board[4] != "X" && board[4] != "O"))
    		check[1] = "5";
    	else if ((board[6] == s && board[7] == s) && (board[8] != "X" && board[8] != "O"))// checking in third row
    		check[1] = "9";
    	else if ((board[7] == s && board[8] == s) && (board[6] != "X" && board[6] != "O"))
    		check[1] = "7";
    	else if ((board[6] == s && board[8] == s) && (board[7] != "X" && board[7] != "O"))
    		check[1] = "8";
    	else if ((board[0] == s && board[3] == s) && (board[6] != "X" && board[6] != "O"))// checking in first column
    		check[1] = "7";
    	else if ((board[3] == s && board[6] == s) && (board[0] != "X" && board[0] != "O"))
    		check[1] = "1";
    	else if ((board[0] == s && board[6] == s) && (board[3] != "X" && board[3] != "O"))
    		check[1] = "4";
    	else if ((board[1] == s && board[4] == s) && (board[7] != "X" && board[7] != "O"))// checking in second column
    		check[1] = "8";
    	else if ((board[4] == s && board[7] == s) && (board[1] != "X" && board[1] != "O"))
    		check[1] = "2";
    	else if ((board[1] == s && board[7] == s) && (board[4] != "X" && board[4] != "O"))
    		check[1] = "5";
    	else if ((board[2] == s && board[5] == s) && (board[8] != "X" && board[8] != "O"))// check in third column
    		check[1] = "9";
    	else if ((board[5] == s && board[8] == s) && (board[2] != "X" && board[2] != "O"))
    		check[1] = "3";
    	else if ((board[2] == s && board[8] == s) && (board[5] != "X" && board[5] != "O"))
    		check[1] = "6";
    	else if ((board[2] == s && board[4] == s) && (board[6] != "X" && board[6] != "O"))// checking in left diagonal
    		check[1] = "7";
    	else if ((board[4] == s && board[6] == s) && (board[2] != "X" && board[2] != "O"))
    		check[1] = "3";
    	else if ((board[2] == s && board[6] == s) && (board[4] != "X" && board[4] != "O"))
    		check[1] = "5";
    	else if ((board[0] == s && board[4] == s) && (board[8] != "X" && board[8] != "O"))// checking in right diagonal
    		check[1] = "9";
    	else if ((board[4] == s && board[8] == s) && (board[0] != "X" && board[0] != "O"))
    		check[1] = "1";
    	else if ((board[0] == s && board[8] == s) && (board[4] != "X" && board[4] != "O"))
    		check[1] = "5";
    	else
    		check[0] = "false"; // otherwise, change element in index 0 to "false"

    	return check;
    }

    public static String[] cpuAttack(int cpuLastMove){ // method for seeing if CPU can place next to its previous place
    	String[] attack = {"true", "0"};
    	if (cpuLastMove == 0){ // if previous move was on 0
    		if (board[1].equals("2") && board[2].equals("3")) // check to see if row/column/diagonal is empty
    			attack[1] = "2"; // determine where to attack
    		else if (board[3].equals("4") && board[6].equals("7")) // same logic below
    			attack[1] = "4";
    		else
    			attack[0] = "false"; // otherwise, change element in index 0 to "false"
    	}
    	else if (cpuLastMove == 1){
    		if (board[0].equals("1") && board[2].equals("3"))
    			attack[1] = "1";
    		else if (board[4].equals("5") && board[7].equals("8"))
    			attack[1] = "5";
    		else
    			attack[0] = "false";
    	}
    	else if (cpuLastMove == 2){
    		if (board[0].equals("1") && board[1].equals("2"))
    			attack[1] = "2";
    		else if (board[5].equals("6") && board[8].equals("9"))
    			attack[1] = "6";
    		else
    			attack[0] = "false";
    	}
    	else if (cpuLastMove == 3){
    		if (board[0].equals("1") && board[6].equals("7"))
    			attack[1] = "1";
    		else if (board[4].equals("5") && board[7].equals("8"))
    			attack[1] = "5";
    		else
    			attack[0] = "false";
    	}
    	else if (cpuLastMove == 4){
    		if (board[1].equals("2") && board[7].equals("8"))
    			attack[1] = "2";
    		else if (board[3].equals("4") && board[5].equals("6"))
    			attack[1] = "4";
    		else if (board[0].equals("1") && board[8].equals("9"))
    			attack[1] = "1";
    		else if (board[2].equals("3") && board[6].equals("7"))
    			attack[1] = "3";
    		else
    			attack[0] = "false";
    	}
    	else if (cpuLastMove == 5){
    		if (board[2].equals("3") && board[8].equals("9"))
    			attack[1] = "3";
    		else if (board[3].equals("4") && board[4].equals("5"))
    			attack[1] = "5";
    		else
    			attack[0] = "false";
    	}
    	else if (cpuLastMove == 6){
    		if (board[0].equals("1") && board[3].equals("4"))
    			attack[1] = "4";
    		else if (board[7].equals("8") && board[8].equals("9"))
    			attack[1] = "8";
    		else
    			attack[0] = "false";
    	}
    	else if (cpuLastMove == 7){
    		if (board[6].equals("7") && board[8].equals("9"))
    			attack[1] = "7";
    		else if (board[4].equals("5") && board[1].equals("2"))
    			attack[1] = "5";
    		else
    			attack[0] = "false";
    	}
    	else if (cpuLastMove == 8){
    		if (board[2].equals("3") && board[5].equals("6"))
    			attack[1] = "6";
    		else if (board[6].equals("7") && board[7].equals("8"))
    			attack[1] = "8";
    		else
    			attack[0] = "false";
    	}

    	return attack;
    }
}
