![](DD.PNG)
 
 *** 
![](II.PNG)

 >### The object of the game is to get numbers 1-9 just once in each of the 9 mini squares and in each vertical  column and horizontal row. So for a 9×9 square Sudoku game:
 >
>  +  each 3×3 box must have numbers 1-9 in any order.
> 
>  +  each column must have numbers 1-9 in any order.
>
>  +  each row must have numbers 1-9 in any order.




![](SSS.PNG)


 

![](BB.PNG)
## Solving Sudoku with some strategic considerations is much easier than you think. In our example we will show you step by step how to do it:

![](1.PNG)
## Divide Sudoku first into 3 horizontal and 3 vertical blocks.


![](11.PNG)

![](2.PNG)
## Search the rows of the individual blocks for recurring numbers. First solutions can often be found this way


![](22.PNG)

![](3.PNG)
## If there is no other solution given, you should examine the intersecting 9-fields.
![](33.PNG)
## Following the same approach used above, there is a further field in which only the number 9 can stand. Using this approach, the entire Sudoku can be solved.
***
>![](QQ.PNG)

We start by creating the class "M1", the main functionnality of this class represent the heart of our Sudoku-Game where we will find the "for" loops that will create the matrixes and other elements.

# M1.h
---
```c++
#ifndef _M1
    #include "Arr.h"
    #define _M1
#endif

namespace sudoku{
    class M1{
		public:
        //Bollean-method that will be used in case the game is  won:
       
		bool you_win();
        void write(int i, int j, int val);
        //used to create a new game:
		void new_puzzle();
        //used to solve the game:
		void solve();
        //used to Readjust the game:
        void reset();
		int read(int i, int j);
        int output(int (*mat)[10]);
        int input(int (*mat)[10]);
        M1(int (*mat)[10]);
        M1();

		private:
        int obj[10][10];
        int row[10];
        int col[10];
        int block[5][5];
        int ne[10][10];
        sudoku::Arr ra;
		int dfs(int ni, int nj);
        void empty();
		void init();
	};
}
```
# M1.cc
---
```c++
#include "M1.h"
//used to adjust the matrix and dividing it to three by three blocks:
void sudoku::M1::init()
{
	for (int i = 0; i < 9; ++i)
	{
		this->row[i] = this->col[i] = 0;
		this->block[i/3][i%3] = 0;
	}
    //to initialize the positions :
	for (int i = 0; i < 9; ++i)
	{
		for (int j = 0; j < 9; ++j)
		{
			if(this->obj[i][j])
			{
				int sig = (1 << (obj[i][j] - 1));
				this->row[i] |= sig;
				this->col[j] |= sig;
				this->block[i/3][j/3] |= sig;
				this->ne[i][j] = 1;
			}
			else this->ne[i][j] = 0;
		}
	}
}
// using for loops to go throught the rows and columns:
int sudoku::M1::input(int (*mat)[10])
{
	for (int i = 0; i < 9; ++i)
	{
		for (int j = 0; j < 9; ++j)
		{
			this->obj[i][j] = mat[i][j];
		}
	}
    return 1;
}

sudoku::M1::M1(int (*mat)[10]):ra()
{
	input(mat);
}

sudoku::M1::M1()
{

}

int sudoku::M1::dfs(int ni, int nj)
{ //used to set the conditions that the numbers need to be less than "9":
	if (this->obj[ni][nj])
	{
		if (nj+1 < 9)
			return dfs(ni, nj+1);
		else if (ni+1 < 9)
			return dfs(ni+1, 0);
		else
			return 1;
	}
	else
	{
		int sig = this->row[ni] | this->col[nj] | this->block[ni/3][nj/3];
		this->ra.narray();
/*using the for lopp to check over all the rows,columns
  and blocks and see if the numbers are not pass the
 number 9 and not repeated */

		for (int i = 0; i < 9; ++i)
		{
			int ri = this->ra.read(i);
			int sign = (1<<(ri-1));
            //seting the conditions with "if":
			if (!(sig & sign))
			{
				this->obj[ni][nj] = ri;
				this->row[ni] |= sign;
				this->col[nj] |= sign;
				this->block[ni/3][nj/3] |= sign;
				if (nj+1 < 9)
				{
					if (dfs(ni, nj+1) > 0)
						return 1;
				}
				else if (ni+1 < 9)
				{
					if (dfs(ni+1, 0) > 0)
						return 1;
				}
				else
				{
					return 1;
				}
				this->obj[ni][nj] = 0;
				this->row[ni] ^= sign;
				this->col[nj] ^= sign;
				this->block[ni/3][nj/3] ^= sign;
			}
		}
		return -1;
	}
}
//if the conditions are met solve the matrix:
void sudoku::M1::solve()
{
    init();
	dfs(0, 0);
}

int sudoku::M1::output(int (*mat)[10])
{
	int res = 1;
	for (int i = 0; i < 9; ++i)
	{
		for (int j = 0; j < 9; ++j)
		{
			mat[i][j] = this->obj[i][j];
			res = res && mat[i][j];
		}
	}
	return res;
}
//used for assignind value to the position that is defined by
void sudoku::M1::write(int i, int j, int val)
{
	if (ne[i][j])
		return;
	this->obj[i][j] = val;
}
//for thevalue of the number that is located on the i/j positions:
int sudoku::M1::read(int i, int j)
{
	return this->obj[i][j];
}
//used to initialize the Grid with the empty-values:
void sudoku::M1::empty()
{
    for (int i = 0; i < 9; ++i)
    {
        this->row[i] = this->col[i] = 0;
        this->block[i/3][i%3] = 0;
    }

    for (int i = 0; i < 9; ++i)
    {
        for (int j = 0; j < 9; ++j)
        {
            this->obj[i][j] = 0;
        }
    }
}
/*using the "for" loops we will check that the conditions are met in order to win
the Game*/
bool sudoku::M1::you_win()
{
	for (int i = 0; i < 9; ++i)
	{
		row[i] = 0;
		col[i] = 0;
		block[i/3][i%3] = 0;
	}
	for (int i = 0; i < 9; ++i)
	{
		for (int j = 0; j < 9; ++j)
		{
			int sig = row[i] | col[j] | block[i/3][j/3];
			int sgn = (1 << (obj[i][j] - 1));
			if (!(sig&sgn))
			{
				row[i] |= sgn;
				col[j] |= sgn;
				block[i/3][j/3] |= sgn;
			}
			else
			{
				return false;
			}
		}
	}
	return true;
}
//used to initialize a new game:
void sudoku::M1::new_puzzle()
{
	empty();
	solve();
	srand(time(NULL));
    int rdbox = rand()%10 + 40;
    //to reset with random values from "1" to "9":
	for (int k = 0; k < rdbox; ++k)
	{
		int i = rand()%9;
		int j = rand()%9;
		while(!obj[i][j])
		{
			i = rand()%9;
			j = rand()%9;
		}
		obj[i][j] = 0;
	}
	init();
}
//Used to give the value "0" to all the numbers except the ones already defined:
void sudoku::M1::reset()
{
    for (int i = 0; i < 9; ++i)
    {
        for (int j = 0; j < 9; ++j)
        {
            if (!ne[i][j])
                obj[i][j] = 0;
        }
    }
}
```
 >   The second class will be called << Arr >> and will
 contains  elements that will allow our Sudoku-Game to have a random values using some arrays :
# Arr.h
---
```c++
#ifndef _Arr
	#include <cstdlib>
	#include <time.h>
    #define _Arr
#endif

namespace sudoku{
    class Arr{
		public:
            Arr();
			void narray();
			int read(int i);

		private:
			int array[10];
			int rdseed;
	};
}
```
# Arr.cc
---
```c++
#include "Arr.h"

void sudoku::Arr::narray()
{
	srand(rdseed);
	this->rdseed = rand();
	int queue[10];
	for (int i = 0; i < 9; ++i)
	{
		queue[i] = i+1;
	}
	for (int i = 0; i < 9; ++i)
	{
		int stp = rand()%9;
		while(!queue[stp])
		{
			stp ++;
			stp %= 9;
		}
		this->array[i] = queue[stp];
		queue[stp] = 0;
	}
}

sudoku::Arr::Arr()
{
	this->rdseed = time(NULL);
	narray();
}

int sudoku::Arr::read(int i)
{
	return this->array[i];
}
```
>We need to add the "include.h" it has some libraries and also the infinity constant that expands to constant expression of type float which evaluates to positive or unsigned infinity :
```c++
#ifndef _INCLUDE
	#include <cstdio>
	#include <iostream>
	#include <algorithm>

	#include "M1.h"
	#define INF 0x3f3f3f3f
	#define _INCLUDE 
#endif

```






---
![](FF.PNG)
![](E.PNG)
![](KK.PNG)
![](H.PNG)
# interface.h
```c++
#include<mainwindow.h>
#include "rules.h"
namespace Ui {
class interface;
}

class interface : public QMainWindow
{
    Q_OBJECT

public:
    explicit interface(QWidget *parent = nullptr);
    ~interface();

private slots:
  //to strat the game 
    void on_pushButton_clicked();
   // to have the rules 
    void on_pushButton_2_clicked();

private:
    Ui::interface *ui;
    MainWindow *w;
    rules *r;
};

#endif

```
# interface.cpp
```c++
#include "interface.h"
#include "ui_interface.h"
#include "rules.h"

interface::interface(QWidget *parent) :
    QMainWindow(parent),
    ui(new Ui::interface)
{
    ui->setupUi(this);
}

interface::~interface()
{
    delete ui;
}

void interface::on_pushButton_clicked()
{
    w= new MainWindow(this);
    w->show();
}


void interface::on_pushButton_2_clicked()
{
    r= new rules(this);
    r->show();
}

```
![](Z.PNG)
![](MM.PNG)
![](H.PNG)
# rules.h
```c++
#ifndef RULES_H
#define RULES_H

#include <QMainWindow>

namespace Ui {
class rules;
}

class rules : public QMainWindow
{
    Q_OBJECT

public:
    explicit rules(QWidget *parent = nullptr);
    ~rules();

private:
    Ui::rules *ui;
};

#endif

```
# rules.cpp
```c++
#include "rules.h"
#include "ui_rules.h"

rules::rules(QWidget *parent) :
    QMainWindow(parent),
    ui(new Ui::rules)
{
    ui->setupUi(this);

}

rules::~rules()
{
    delete ui;
}

```
![](A.PNG)
![](LL.PNG)
![](H.PNG)
# mainwindow.h
```c++
#ifndef MAINWINDOW_H
#define MAINWINDOW_H
#include <QMainWindow>
#include <QLabel>
#include <QString>
#include <QTableWidget>
#include "include.h"
#include "rules.h"

namespace Ui {
class MainWindow;
}

class MainWindow : public QMainWindow
{
    Q_OBJECT

public:
    explicit MainWindow(QWidget *parent = 0);
    ~MainWindow();
//used for connecting the buttons of the Mainwindow:
private slots:
    void on_pb00_1_clicked();//for the button number "1"
    void on_pb00_2_clicked();//for the button number "2"
    void on_pb00_3_clicked();//for the button number "3"
    void on_pb00_4_clicked();//for the button number "4"
    void on_pb00_5_clicked();//for the button number "5"
    void on_pb00_6_clicked();//for the button number "6"
    void on_pb00_7_clicked();//for the button number "7"
    void on_pb00_8_clicked();//for the button number "8"
    void on_pb00_9_clicked();//for the button number "9"
    //used to show the exact positioning of the cell clicked in the table:
    void on_table_cellClicked(int row, int column);
    //the button used to solve the game:
    void on_solve_clicked();
    // for starting a New-Game:
    void on_actionNew_game_triggered();
    //used to give the rules of the game:
    void on_pushButton_clicked();
    //Also used to give the rules of the game:
    void on_pushButton_2_clicked();

private:
    Ui::MainWindow *ui;
    int Column, Row;
    //to click on the numbers:
    void click__on_pb(int num);
    rules *t;
};

#endif
//We can find the implementation of the methods in the mainwindow.cpp;

```
# mainwindow.cpp
```c++
#include "mainwindow.h"
#include "ui_mainwindow.h"

sudoku::M1 matx;

MainWindow::MainWindow(QWidget *parent) :
    QMainWindow(parent),
    ui(new Ui::MainWindow)
{
    ui->setupUi(this);
    matx.new_puzzle();
    for (int i = 0; i < 9; ++i)
    {
        for (int j = 0; j < 9; ++j)
        {
            QTableWidgetItem* Cell = ui->table->item(i, j);
            QString str = "";
            str += '0' + matx.read(i, j);
            const QString cstr = str;
            Cell->setText(cstr);
            if (matx.read(i, j))
            {
                QColor c(0,191,255);
                Cell->setBackgroundColor(c);
            }
        }
    }
    ui->label->setText("sudoku!!");
}

MainWindow::~MainWindow()
{
    delete ui;
}

void MainWindow::on_table_cellClicked(int row, int column)
{
    Row = row;
    Column = column;
    QString str = "";
    str += '0' + row;
    str += ',';
    str += '0' + column;
    const QString cstr = str;
    ui->label->setText(cstr);
}

void MainWindow::click__on_pb(int num)
{
    QTableWidgetItem* Cell = ui->table->item(Row, Column);
    matx.write(Row, Column, num);
    QString str = "";
    str += '0' + matx.read(Row, Column);
    const QString cstr = str;
    Cell->setText(cstr);
    /*the message you will receive in case the conditions of
      "you-win()"are met wich means you have won the game;*/
    if (matx.you_win())
    {
        ui->label->setText("You just won the Game");
    }
}
//used to give the number 1 if the pushbutton 1 is clicked:
void MainWindow::on_pb00_1_clicked()
{
   click__on_pb(1);
}
//used to give the number 2 if the pushbutton 2 is clicked:
void MainWindow::on_pb00_2_clicked()
{
    click__on_pb(2);
}
//used to give the number 3 if the pushbutton 3 is clicked:
void MainWindow::on_pb00_3_clicked()
{
    click__on_pb(3);
}
//used to give the number 4 if the pushbutton 4 is clicked:
void MainWindow::on_pb00_4_clicked()
{
    click__on_pb(4);
}
//used to give the number 5 if the pushbutton 5 is clicked:
void MainWindow::on_pb00_5_clicked()
{
    click__on_pb(5);
}
//used to give the number 6 if the pushbutton 6 is clicked:
void MainWindow::on_pb00_6_clicked()
{
    click__on_pb(6);
}
//used to give the number 7 if the pushbutton 7 is clicked:
void MainWindow::on_pb00_7_clicked()
{
    click__on_pb(7);
}
//used to give the number 8 if the pushbutton 8 is clicked:
void MainWindow::on_pb00_8_clicked()
{
    click__on_pb(8);
}
//used to give the number 9 if the pushbutton 9 is clicked:
void MainWindow::on_pb00_9_clicked()
{
    click__on_pb(9);
}
//used to solve the game:
void MainWindow::on_solve_clicked()
{
    matx.reset();
    matx.solve();
    for (int i = 0; i < 9; ++i)
	{
        for (int j = 0; j < 9; ++j)
		{
			QTableWidgetItem* Cell = ui->table->item(i, j);
			QString str = "";
            str += '0' + matx.read(i,j);
			const QString cstr = str;
			Cell->setText(cstr);
		}
	}
}
// used to set a new game if clicked:
void MainWindow::on_actionNew_game_triggered()
{
    matx.new_puzzle();
    for (int i = 0; i < 9; ++i)
    {
        for (int j = 0; j < 9; ++j)
        {
            QTableWidgetItem* Cell = ui->table->item(i, j);
            QString str = "";
            str += '0' + matx.read(i, j);
            const QString cstr = str;
            Cell->setText(cstr);
            if (matx.read(i, j))
            {//used to set the background  colors of the fixed numbers:
                QColor c(0,191,255);
                Cell->setBackgroundColor(c);
            }
            else
            {//used to set the background color of the numbers:
                QColor c(250,250,250);
                Cell->setBackgroundColor(c);
            }
        }
    }
}

void MainWindow::on_pushButton_clicked()
{
    matx.reset();
    for (int i = 0; i < 9; ++i)
    {
        for (int j = 0; j < 9; ++j)
        {
            QTableWidgetItem* Cell = ui->table->item(i, j);
            QString str = "";
            str += '0' + matx.read(i, j);
            const QString cstr = str;
            Cell->setText(cstr);
        }
    }
}

```
![](F.PNG)

>![](G2.GIF)
![](G1.GIF)
![](CCC.PNG)
   
>   ## Made by: YASSINE SQUALLI HOUSSAINI






