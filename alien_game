#include "library.h"
#include <iostream>
#include <fstream>
#include <string>

using namespace std;

image * robotIcon = image_from_file("robot_lab_12 - Copy.bmp");
image * alienIcon = image_from_file("Alien.bmp");
image * shipIcon = image_from_file("ship - Copy.bmp");
image * homeIcon = image_from_file("robotHome.bmp");

void makingMaze(int numRows, int numColumns, char maze[][31], int penWidthHalf){
	set_pen_width(penWidthHalf * 2);
	penWidthHalf = penWidthHalf / 2;
	int sizeOfSquare = 40;
	double windowY = (numRows * penWidthHalf) + (sizeOfSquare * (numRows));
	double windowX = (numColumns * penWidthHalf) + (sizeOfSquare * (numColumns));
	make_window(windowX, windowY  + 160);

	for (int lineRows = 0; lineRows <= windowY; lineRows += (sizeOfSquare + penWidthHalf)){
		move_to(0, lineRows);
		draw_to(windowX, lineRows * 1.0);
	}
	for (int lineColumns = 0; lineColumns <= windowX; lineColumns += (sizeOfSquare + penWidthHalf)){
		move_to(lineColumns, 0);
		draw_to(lineColumns * 1.0, windowY);
	}

	//Coloring the boxes
	for (int rows = 0; rows < numRows; rows++){
		for (int columns = 0; columns < numColumns; columns++){
			double x = columns * sizeOfSquare + penWidthHalf * (columns + 1), y = rows * sizeOfSquare + penWidthHalf * (rows + 1);
			if (maze[rows][columns] == '#'){
				fill_rectangle(x, y, sizeOfSquare * 1.0, sizeOfSquare * 1.0, color::grey);
			}
			if (maze[rows][columns] == '+'){
				draw_image(shipIcon, x, y);
			}
			if (maze[rows][columns] == '$')
				draw_image(homeIcon, x, y);
		}
	}

	//Creating a space where the player can see the possible functions
	set_font("Ariel", 80);
	set_pen_color(color::indigo);
	move_to(10.0, windowY + 10);
	set_font_size(20);
	write_string("Up = 'W'", direction::east);
	move_to(10.0, windowY + 30);
	write_string("Left = 'A'", direction::east);
	move_to(10.0, windowY + 50);
	write_string("Down = 'S'", direction::east);
	move_to(10.0, windowY + 70);
	write_string("Right = 'D'", direction::east);
	move_to(10.0, windowY + 90);
	write_string("Back A Step = 'B'", direction::east);
	move_to(10.0, windowY + 110);
	write_string("Robot mode = 'R'", direction::east);
	move_to(10.0, windowY + 130);
	write_string("Manual mode = 'M'", direction::east);
	move_to(10.0, windowY + 150);
	write_string("To quit the game = 'Q'", direction::east);
}

void endScreen(string end, int numRows, int numColumns, int sizeOfSquare, int penWidthHalf){
	double boxWidth = 400, boxHeight = 150;
	double windowY = (numRows * penWidthHalf) + (sizeOfSquare * (numRows)) ;
	double windowX = (numColumns * penWidthHalf) + (sizeOfSquare * (numColumns)) - boxWidth ;

	move_to(windowX, windowY + (boxHeight / 2));
	set_pen_color(color::indigo);
	set_font("Ariel", 80);
	write_string(end, direction::center);
}

int person(char arra[][31], int shiftX, int shiftY, int column, int row, int numRows, int numColumns, int sizeOfSquare, int penWidthHalf, int value, int & step){
	double x = column * sizeOfSquare + penWidthHalf * (column + 1) , y = row * sizeOfSquare + penWidthHalf * (row + 1) ;
	if (arra[row + shiftY][column + shiftX] != '#'){
		if (arra[row][column] == '$')
			draw_image(homeIcon, x, y);
		else if (arra[row][column] == '+')
			draw_image(shipIcon, x, y);
		else
			fill_rectangle(x, y, sizeOfSquare * 1.0, sizeOfSquare * 1.0, color::white);

		
		x = x + (shiftX * (penWidthHalf + sizeOfSquare)), y = y + (shiftY * (penWidthHalf + sizeOfSquare));

		if (value == 22)
			draw_image(robotIcon, x, y);
		else if (value == 13)
			draw_image(alienIcon, x, y);
		step = 1;

		int c = column + shiftX, r = row + shiftY;
		if (shiftX != 0 && shiftY == 0)
			return c;
		else if (shiftY != 0 && shiftX == 0)
			return r;
	}
	else {
		if (shiftX != 0 && shiftY == 0)
			return column;
		else if (shiftY != 0 && shiftX == 0)
			return row;
	}
}


//This is the original function I used to retrace its steps but it caused it to glitch. I created another instead
void findingNextGreatest(int steps[][31], int & currentStep, int numRows, int numColumns, int & greatestRow, int & greatestColumn){
	int greatest = 0;
	
	for (int rows = 0; rows < numRows; rows++){
		for (int columns = 0; columns < numColumns; columns++){
			if (greatest < steps[rows][columns] && steps[rows][columns] <= (currentStep - 1)){
				greatest = steps[rows][columns];
				greatestRow = rows;
				greatestColumn = columns;
			}
		}
	}

	currentStep = greatest;
}

void findingLastStep(int steps[][31], int row, int column, int & shiftX, int & shiftY){
	int currentStep = steps[row][column];
	for (int i = 1; i < 100; i++){
		if (steps[row - 1][column] == currentStep - i){
			shiftY = -1, shiftX = 0;
			break;
		}
		else if (steps[row + 1][column] == currentStep - i){
			shiftY = 1, shiftX = 0;
			break;
		}
		else if (steps[row][column - 1] == currentStep - i){
			shiftX = -1, shiftY = 0;
			break;
		}
		else if (steps[row][column + 1] == currentStep - i){
			shiftX = 1, shiftY = 0;
			break;
		}
	}
}

void enemy(char arra[][31], int enemyPath[][31], int & currentStep, int & column, int & row, int numRows, int numColumns, int penWidthHalf, int sizeOfSquare){
	int move = 1, addAStep = 0, greatestRow = 0, greatestColumn = 0, shiftX = 0, shiftY = 0; 
	bool didItGoBack = false;
	switch (move){
	case 1:
		if (arra[row - 1][column] != '#' && enemyPath[row - 1][column] == 0){
			row = person(arra, 0, -1, column, row, numRows, numColumns, sizeOfSquare, penWidthHalf, 13, addAStep);
			break;
		}
	case 2:
		if (arra[row + 1][column] != '#' && enemyPath[row + 1][column] == 0){
			row = person(arra, 0, 1, column, row, numRows, numColumns, sizeOfSquare, penWidthHalf, 13, addAStep);
			break;
		}
	case 3:
		if (arra[row][column - 1] != '#' && enemyPath[row][column - 1] == 0){
			column = person(arra, -1, 0, column, row, numRows, numColumns, sizeOfSquare, penWidthHalf, 13, addAStep);
			break;
		}
	case 4:
		if (arra[row][column + 1] != '#' && enemyPath[row][column + 1] == 0){
			column = person(arra, 1, 0, column, row, numRows, numColumns, sizeOfSquare, penWidthHalf, 13, addAStep);
			break;
		}
	case 5:
		findingLastStep(enemyPath, row, column, shiftX, shiftY);
		if (shiftX != 0)
			column = person(arra, shiftX, shiftY, column, row, numRows, numColumns, sizeOfSquare, penWidthHalf, 13, addAStep);
		else if (shiftY != 0)
			row = person(arra, shiftX, shiftY, column, row, numRows, numColumns, sizeOfSquare, penWidthHalf, 13, addAStep);
		didItGoBack = true;
		break;
		
	default:
		break;
	}
	if (didItGoBack != true && addAStep == 1){
		currentStep += addAStep;
		enemyPath[row][column] = currentStep;
	}
}

void automaticRobot(char arra[][31], int steps[][31], int enemyPath[][31], int & currentStep, int & currentEnemyStep, int & column, int & row, int & enemyColumn, int & enemyRow, int numColumns, int numRows, int penWidthHalf, int sizeOfSquare){
	int addAStep = 0, greatestRow = 0, greatestColumn = 0, shiftX = 0, shiftY = 0;
	while (true) {
		char button2 = wait_for_key_typed(0.1);
		if (button2 != 'm' && button2 != 'q') {
			int move = 1; 
			bool didItGoBack = false;
			switch (move){
			case 1:
				if (arra[row - 1][column] != '#' && steps[row - 1][column] == 0){
					row = person(arra, 0, -1, column, row, numRows, numColumns, sizeOfSquare, penWidthHalf, 22, addAStep);
					break;
				}
			case 2:
				if (arra[row + 1][column] != '#' && steps[row + 1][column] == 0){
					row = person(arra, 0, 1, column, row, numRows, numColumns, sizeOfSquare, penWidthHalf, 22, addAStep);
					break;
				}
				else
			case 3:
				if (arra[row][column - 1] != '#' && steps[row][column - 1] == 0){
					column = person(arra, -1, 0, column, row, numRows, numColumns, sizeOfSquare, penWidthHalf, 22, addAStep);
					break;
				}
				else
			case 4:
				if (arra[row][column + 1] != '#' && steps[row][column + 1] == 0){
					column = person(arra, 1, 0, column, row, numRows, numColumns, sizeOfSquare, penWidthHalf, 22, addAStep);
					break;
				}
				else
			case 5:
				if (arra[row][column] == '$')
					break;
				else {
					findingLastStep(steps, row, column, shiftX, shiftY);
					if (shiftX != 0)
						column = person(arra, shiftX, shiftY, column, row, numRows, numColumns, sizeOfSquare, penWidthHalf, 22, addAStep);
					else if (shiftY != 0)
						row = person(arra, shiftX, shiftY, column, row, numRows, numColumns, sizeOfSquare, penWidthHalf, 22, addAStep);
					didItGoBack = true;
					break;
				}
			default:
				break;
			}
			if (didItGoBack != true && addAStep == 1){
				currentStep += addAStep;
				steps[row][column] += currentStep;
				currentStep = steps[row][column];
			}
			enemy(arra, enemyPath, currentEnemyStep, enemyColumn, enemyRow, numRows, numColumns, penWidthHalf, sizeOfSquare);
		}
		else if (button2 == 'q'){
			arra[row][column] = '%';
			break;
		}
		else if (button2 = 'm')
			break;
		if (column == enemyColumn && row == enemyRow)
			break;
		else if (arra[row][column] == '+')
			break;
		
	}
}

void movingRobot(char arra[][31], int numRows, int numColumns, int penWidthHalf, int sizeOfSquare, int steps[][31], int enemyPath[][31]){
	int column, row, enemyColumn = 0, enemyRow = 0, currentEnemyStep = 1, currentStep = 1, placeHolder = 0;
	for (int rows = 0; rows < numRows; rows++){
		for (int columns = 0; columns < numColumns; columns++){
			if (arra[rows][columns] == '$'){
				column = columns;
				row = rows;
			}
			if (arra[rows][columns] == 'E'){
				enemyColumn = columns;
				enemyRow = rows;
			}
		}
	}
	person(arra, 0, 0, column, row, numRows, numColumns, sizeOfSquare, penWidthHalf, 22, placeHolder);
	person(arra, 0, 0, enemyColumn, enemyRow, numRows, numColumns, sizeOfSquare, penWidthHalf, 13, placeHolder);
	steps[row][column] = currentStep;
	enemyPath[enemyRow][enemyColumn] = currentEnemyStep;
	while (true){
		bool didItGoBack = false;
		int addAStep = 0, greatestRow = 0, greatestColumn = 0, shiftX, shiftY;
		char button = wait_for_key_typed();
		switch (button){
		case 'w':
			row = person(arra, 0, -1, column, row, numRows, numColumns, sizeOfSquare, penWidthHalf, 22, addAStep);
			break;
		case 's':
			row = person(arra, 0, 1, column, row, numRows, numColumns, sizeOfSquare, penWidthHalf, 22, addAStep);
			break;
		case 'a':
			column = person(arra, -1, 0, column, row, numRows, numColumns, sizeOfSquare, penWidthHalf, 22, addAStep);
			break;
		case 'd':
			column = person(arra, 1, 0, column, row, numRows, numColumns, sizeOfSquare, penWidthHalf, 22, addAStep);
			break;
		case 'r':
			automaticRobot(arra, steps, enemyPath, currentStep, currentEnemyStep, column, row, enemyColumn, enemyRow, numColumns, numRows, penWidthHalf, sizeOfSquare);
			break;
		case 'q':
			arra[row][column] = '%';
			break;

		// In this case, the robot will try to retrace its steps
		case 'b':
			if (arra[row][column] == '$')
				break;
			else {
				findingLastStep(steps, row, column, shiftX, shiftY);
				steps[row][column] = 0;
				if (shiftX != 0)
					column = person(arra, shiftX, shiftY, column, row, numRows, numColumns, sizeOfSquare, penWidthHalf, 22, addAStep);
				else if (shiftY != 0)
					row = person(arra, shiftX, shiftY, column, row, numRows, numColumns, sizeOfSquare, penWidthHalf, 22, addAStep);
				didItGoBack = true;
				break;
			}

		default:
			break;
		}
		enemy(arra, enemyPath, currentEnemyStep, enemyColumn, enemyRow, numRows, numColumns, penWidthHalf, sizeOfSquare);
		//cout << " enemy: " << enemyRow << " " << enemyColumn << "you: " << row << " " << column << endl;
		if (arra[row][column] == '+'){
			endScreen("You Won!", numRows, numColumns, sizeOfSquare, penWidthHalf);
			break;
		}
		else if (column == enemyColumn && row == enemyRow){
			endScreen("You Lost!", numRows, numColumns, sizeOfSquare, penWidthHalf);
			break;
		}
		else if (arra[row][column] == '%'){
			endScreen("Game Ended", numRows, numColumns, sizeOfSquare, penWidthHalf);
			break;
		}
		else if (didItGoBack != true && addAStep == 1){
			currentStep += addAStep;
			steps[row][column] += currentStep;
			currentStep = steps[row][column];
		}
		
	}
	for (int rows = 0; rows < numRows; rows++){
		for (int columns = 0; columns < numColumns; columns++){
			if (steps[rows][columns] != 0){
				person(arra, 0, 0, columns, rows, numRows, numColumns, sizeOfSquare, penWidthHalf, 9, placeHolder);
			}
		}
	}
}

void main(){
	cout << "Hello! The goal of this game is to get the robot from his home to the spaceship without getting caught by the alien." << endl;
	cout << "The commands are: \n  Up = 'W' \n  Left = 'A' \n  Down = 'S' \n  Right = 'D' \n  Back A Step = 'B' \n";
	cout << "To go to: \n  Robot mode = 'R' \n  Manual mode = 'M' \nTo quit the game = 'Q'" << endl;

	ifstream fi;
	fi.open("maze.txt");
	if (fi.fail()){
		cout << "Couldn't open file :(";
		exit(1);
	}
	const int numRows = 11;
	const int numColumns = 31;
	char maze[numRows][numColumns]; 
	int steps[numRows][numColumns] = { 0 }, enemyPath[numRows][numColumns] = { 0 };
	
	for (int rows = 0; rows < numRows; rows++){
		for (int columns = 0; columns < numColumns; columns++){
			char c;
			fi >> c;
			maze[rows][columns] = c;
		}
	}
	fi.close();

	//I added a varible of pen size and square size because I wanted to make it bigger or smaller
	int penWidthHalf = 2;
	int sizeOfSquare = 40;
	makingMaze(numRows, numColumns, maze, penWidthHalf);
	movingRobot(maze, numRows, numColumns, penWidthHalf / 2, 40, steps, enemyPath);
	

	}
