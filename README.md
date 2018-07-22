# Chess.cs
Was looking for a C# chessboard move validation API but could not find one, so I ported and adapted Chess.js https://github.com/jhlywa/chess.js

# API
using ChessValidator;

## Constructor: Chess(string fen)
The Chess() constructor takes an optional parameter which specifies the board configuration in Forsyth-Edwards Notation.
```C#
Chess chess=new Chess();
Chess chess=new Chess("r1k4r/p2nb1p1/2b4p/1p1n1p2/2PP4/3Q1NB1/1P3PPP/R5K1 b - c3 0 19");
```

## string Ascii()
Returns a string containing an ASCII diagram of the current position.
```C#
Chess chess = new Chess();

// make some moves
chess.Move("e4");
chess.Move("e5");
chess.Move("f4");

Debug.Write(chess.Ascii());
//returns
// -> '   +------------------------+
//      8 | r  n  b  q  k  b  n  r |
//      7 | p  p  p  p  .  p  p  p |
//      6 | .  .  .  .  .  .  .  . |
//      5 | .  .  .  .  p  .  .  . |
//      4 | .  .  .  .  P  P  .  . |
//      3 | .  .  .  .  .  .  .  . |
//      2 | P  P  P  P  .  .  P  P |
//      1 | R  N  B  Q  K  B  N  R |
//        +------------------------+
//          a  b  c  d  e  f  g  h'
```

## Clear()
Removes all pieces from the board.
```C#
Chess chess = new Chess();
chess.Clear(); // board is now empty
```

## string Fen()
Returns a FEN string for the current position.
```C#
Chess chess = new Chess();

// make some moves
chess.Move("e4");
chess.Move("e5");
chess.Move("f4");

string fen=chess.Fen();
Debug.Write(fen); //Returns 'rnbqkbnr/pppp1ppp/8/4p3/4PP2/8/PPPP2PP/RNBQKBNR b KQkq f3 0 2'
```

## bool GameOver()
Returns true if the game has ended via checkmate, stalemate, draw, threefold repetition, or insufficient material. 
Otherwise, returns false.
```C#
Chess chess = new Chess();
chess.GameOver(); // fresh board game not over returns false

chess.LoadFen("4k3/4P3/4K3/8/8/8/8/8 b - - 0 78");
chess.GameOver(); // this is stalemate so returns true

chess.LoadFen("rnb1kbnr/pppp1ppp/8/4p3/5PPq/8/PPPPP2P/RNBQKBNR w KQkq - 1 3");
chess.GameOver(); // this is checkmate so returns true
```

## Piece GetPiece(string square)
Returns the piece on the square otherwise returns null if empty square or invalid square.
```C#
Chess chess = new Chess();
Piece piece=chess.GetPiece("a1");
Debug.Write(piece.type+" "+piece.color); // white rook is on a1 so returns piece.type='r' and piece.color='w'
Piece piece=chess.GetPiece("a4"); // returns null as no piece on a4
```

## bool inCheck()
Returns true or false if the side to move is is check.

## bool inCheckmate()
Returns true or false if the side to move is in checkmate.

## bool InDraw()
Returns true or false if the game is drawn ( 50 move rule , insuffcient material, three fold rep, stalemate )

## bool InStalemate()
Returns true or false if the side to move has been stalemated.

## bool InThreefoldRepetition()
Returns true or false if the current board position has occurred three or more times
```C#
Chess chess = new Chess("rnbqkbnr/pppppppp/8/8/8/8/PPPPPPPP/RNBQKBNR w KQkq - 0 1");

// rnbqkbnr/pppppppp/8/8/8/8/PPPPPPPP/RNBQKBNR w KQkq occurs 1st time
chess.InThreefoldRepetition();
// -> false

chess.Move("Nf3"); chess.Move("Nf6"); chess.Move("Ng1"); chess.Move("Ng8");
// rnbqkbnr/pppppppp/8/8/8/8/PPPPPPPP/RNBQKBNR w KQkq occurs 2nd time
chess.InThreefoldRepetition();
// -> false

chess.Move("Nf3"); chess.Move("Nf6"); chess.Move("Ng1"); chess.Move("Ng8");
// rnbqkbnr/pppppppp/8/8/8/8/PPPPPPPP/RNBQKBNR w KQkq occurs 3rd time
chess.InThreefoldRepetition();
// -> true
```

## bool InsufficientMaterial()
Returns true if the game is drawn due to insufficient material (K vs. K, K vs. KB, or K vs. KN) etc... ; otherwise false.

## bool LoadFen(string fen)
The board is cleared, and the FEN string is loaded. Returns true if the position was successfully loaded, otherwise false.
```C#
Chess chess = new Chess();
chess.LoadFen("4r3/8/2p2PPk/1p6/pP2p1R1/P1B5/2P2K2/3r4 w - - 1 45"); // returns true

chess.LoadFen("4r3/8/X12XPk/1p6/pP2p1R1/P1B5/2P2K2/3r4 w - - 1 45"); // returns false, bad piece X
```

## bool Move(string move,bool sloppy=false)
Attempts to make a move on the board, returning 
true if move was legal, otherwise false.
Can be called by passing in a string move Standard Algebraic Notation (SAN) like so
```C#
Chess chess = new Chess();
chess.Move("e4"); 
chess.Move("nf6"); \\ returns false SAN is case sensetive !
chess.Move("Nf6"); \\ correct
chess.Move("O-O"; \\ Castle king side
chess.Move("O-O-O"); \\ Castle queen side
```
or by setting bool sloppy to true, to parse a variety of non-standard mov enotations like so
```C#
Chess chess = new Chess();
chess.Move("e2e4",true);
chess.Move("e7-e5",true);
chess.Move("Pf2f4",true);
chess.Move("Pe5xf4",true);
```

## string[] MoveHistory()
Returns a string array of the current game history
```C#
Chess chess = new Chess();
chess.move("e4");
chess.move("e5");
chess.move("f4");
chess.move("exf4");
string[] p=chess.MoveHistory(); // returns {'e4', 'e5', 'f4', 'exf4'}
```

## string[] LegalMovesAll()
Returns a string array in SAN for all legal moves for the current chess board position
```C#
Chess chess = new Chess(); 
string[] moves = chess.LegalMovesAll();
// Returns :
// {'a3', 'a4', 'b3', 'b4', 'c3', 'c4', 'd3', 'd4', 'e3', 'e4',
//'f3', 'f4', 'g3', 'g4', 'h3', 'h4', 'Na3', 'Nc3', 'Nf3', 'Nh3'}
// Fresh board all legal moves for white.
// If no legal moves array will be empty i.e moves.Length=0
```

## string[] LegalMovesSquare(string sqaure)
As above except returns all legal moves for a given square.
```C#
Chess chess = new Chess(); 
string[] moves = chess.LegalMovesSquare("e2");
// Returns:
// { 'e3', 'e4' }
// Fresh board theres only 2 legal moves for pawn on e2, e3 and e4.
// If no legal moves returned array will be empty i.e e5 is empty sqaure, Rook on a1 has no where to go.
```

## bool Put(Piece piece, string square)
Attempts to place a piece on a square, returns true if successful or false if invalid
square or when 2 or more kings of the same color are placed
```C#
chess = new Chess();
chess.Clear();
Piece piece = new Piece() { type = "r", color = "w" }; // create a white rook piece
chess.Put(piece, "a1"); // returns true white rook placed on a1
```

## Piece Remove(string sqaure)
Attempts to remove a piece from the board, returns Piece if successful
otherwise null if empty square or invalid square.
```C#
Chess = new Chess();
chess.Clear();
Piece piece=chess.Remove("a1"); // returns Piece=null empty square

chess = new Chess();
Piece piece=chess.Remove("e2"); // returns Piece = { type="p" color="w" } white pawn on e2
```

## Reset()
Resets board to the initial starting position.

## string SquareColor(string square)
Retuns the color of the square ( 'light' or 'dark')
```C#
Chess chess = Chess();
string c=chess.SquareColor("h1") // retuns 'light'
```

## string Turn()
Returns the current side to move
```C#
Chess chess = Chess();
string turn=chess.Turn(); // fresh board white turn returns "w" 
```

## UndoMoveArgs Undo()
Takeback the last half move and return details to UndoMoveArgs object if not successful UndoMoveArgs object returns null.
```C#
Chess chess = new Chess();
chess.Move("e4");
UndoMoveArgs undoMoveArgs = chess.Undo();

// Returms to undoMoveArgs.color="w", undoMoveArgs.from="e2", undoMoveArgs.to="e4", undoMoveArgs.piece="w"
```

## ValidateFenArgs ValidateFen(string fen)
Validates a FEN returns details to ValidateFenArgs object.
```C#
Chess chess=new Chess();
ValidateFenArgs validateFenArgs= chess.ValidateFen("4r3/8/X12XPk/1p6/pP2p1R1/P1B5/2P2K2/3r4 w - - 1 45");

validateFenArgs.success   // bool true if valid fen otherwise false
validateFenArgs.errorMessage // stirng ocntains the error message 
validateFenArgs.validatedFen // string containg validated fen if valid fen
```





