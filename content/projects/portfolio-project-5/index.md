---
title: Real Tic Tac Toe
seo_title: Real Tic Tac Toe
summary: Puzzle Game.
description: Puzzle Game.
slug: real-tic-tac-toe
author: Himanshu

draft: false
date: 2019-02-20T03:52:30-05:00
lastmod: 
expiryDate: 
publishDate: 

feature_image: tic_tac_toe.png
feature_video: videos/tic_tac_toe.mp4
feature_image_alt: Real Tic Tac Toe

project types: 
    - Personal

techstack:
    - Unity
    - GIMP
live_url: https://chungununo1.itch.io/tic-tac-toe
source_url: https://github.com/Heemo2000/Tic-Tac-Toe
---

This is the puzzle game based on tic tac toe game. Minimax algorithm is used for the implementation of computer's AI.

Here are some more technical details of the game:

* Implemented Alpha Beta prunning to optimize calculating moves.

* Implemented difficulty levels by choosing between random move and perfect move.


## Code Blocks

### Code block for AI logic

```C#
private void ChooseMove(float randomMoveProbability)
{

	if(CountVacants() == 0)
	{
		return;
	}
	Random.InitState((int)System.DateTime.Now.Ticks);
	
    float probability = Random.value;
	
    Debug.Log("Probability: " + probability);
	
    if(probability <= randomMoveProbability)
	{
		RandomMove();
	}
	else
	{
		BestMove();
	}
}

private void RandomMove()
{
	Debug.Log("RandomMove called");
	
    Random.InitState((int)System.DateTime.Now.Ticks);
	
    int moveX = Random.Range(0,3);
	int moveY = Random.Range(0,3);
	
    while(_board[moveX][moveY] != PlayerType.None)
	{
		Debug.Log("RandomMove running");
		moveX = Random.Range(0,3);
		moveY = Random.Range(0,3);
	}
	
    _board[moveX][moveY] = PlayerType.Computer;
	
    StartCoroutine(PrintEnemyMove(moveX, moveY));
	
}
private void BestMove()
{
	_functionCalls = 0;
	_maxDepth = 0;
	int bestScore = int.MinValue;
	int moveX = -1;
	int moveY = -1;
	for (int i = 0; i < 3; i++)
	{
	  for (int j = 0; j < 3; j++)
	  {
			 if (_board[i][j] == PlayerType.None)
			 {
				 _board[i][j] = PlayerType.Computer;
				 int score = Minimax(0, false,int.MinValue,int.MaxValue);
				 _board[i][j] = PlayerType.None;
				 if (score > bestScore)
				 {
					 bestScore = score;
					 moveX = i;
					 moveY = j;
				 }
			 }
	   }
	}
	Debug.Log("Minimax Function Calls: " + _functionCalls);
	Debug.Log("Max Depth: " + _maxDepth);
	if(moveX != -1 && moveY != -1)
	{
		_board[moveX][moveY] = PlayerType.Computer;
		StartCoroutine(PrintEnemyMove(moveX, moveY));
	}
		 
}

int Minimax(int depth, bool isMaximizing,int alpha, int beta)
{
	 _maxDepth = Mathf.Max(_maxDepth, depth);
	 _functionCalls++;
	 if (Winning(PlayerType.Human))
	 { 
		 return -10;
	 }
	 else if (Winning(PlayerType.Computer))
	 { 
		 return 10;
	 }
     else if (IsItATie())
	 {
		 return 0;
	 }

	 int bestScore = -1;
	 if (isMaximizing)
	 {
		 bestScore = int.MinValue;
		 for (int i = 0; i < 3; i++)
		 {
			 for (int j = 0; j < 3; j++)
			 {
				 if (_board[i][j] == PlayerType.None)
				 {
					 _board[i][j] = PlayerType.Computer;
					 int score = Minimax(depth + 1, false,alpha,beta);
					 bestScore = Mathf.Max(bestScore, score);
					 _board[i][j] = PlayerType.None;

					 alpha = Mathf.Max(alpha, bestScore);
					 if (beta <= alpha)
					 {
						 break;
					 }
				 }
			 }
		 }
		 return bestScore;
	 }
	 else
	 {
		 bestScore = int.MaxValue;
		 for (int i = 0; i < 3; i++)
		 {
			 for (int j = 0; j < 3; j++)
			 {
				 if (_board[i][j] == PlayerType.None)
				 {
					 _board[i][j] = PlayerType.Human;
					 int score = Minimax(depth + 1, true, alpha, beta);
					 bestScore = Mathf.Min(bestScore, score);
					 _board[i][j] = PlayerType.None;

					 beta = Mathf.Min(beta, bestScore);
					 if (beta <= alpha)
					 {
						 break;
					 }
				 }
			 }
		 }
		 return bestScore;
	 }
		 
}

```