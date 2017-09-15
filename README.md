# five-in-a-row
#include <stdio.h>
#include <graphics.h>      // 引用图形库头文件
#include <conio.h>
#include <windows.h>
#include <windows.h>

#define KEY_DOWN(vk_c) (GetAsyncKeyState(vk_c)&0x8000?1:0)

typedef struct position
{
	int x;
	int y;
}postype;

postype cur;           //当前光标位置
postype chess;
int index;
           

char number[][3] = { "1","2", "3", "4", "5",
"6", "7", "8", "9", "10",
"11", "12", "13", "14", "15",
"16", "17", "18", "19" };

char bnumber[][20] = { "  一","  二", "  三", "  四", "  五", 
"  六", "  七", "  八", "  九", "  十","十一", "十二", 
"十三", "十四", "十五","十六", "十七", "十八", "十九" };

void Drawchessboard();    //绘制棋盘
int Playgame();          //游戏进程
void Drawcursor(postype cur, COLORREF cl);         //绘制光标
void Showsteps(int a);        //落子数
int isValid();
void Gameover();


void main()
{
	cur.x = 515; cur.y = 340;
	initgraph(1000, 650);   // 创建绘图窗口，大小为 1000,650像素
	Drawchessboard();       
	Drawcursor(cur,BLACK);
	Playgame();
 
	
}

void Drawchessboard()
{
	int i=0,j,k,x=0,y=0;
	setfillcolor(RGB(218,144,35));
	solidrectangle(215,40,815,640);
	fillrectangle(243,68,787,612);//绘制棋盘边界
	for (int Line = 0; Line< 19; Line++)
	{
		line(245 + i,70 ,245 + i,610 );//绘制竖线
		line(245 ,70 + i,785 ,70 + i);//绘制横线
		i += 30;
	}
	for (j = 0; j < 3; j++)
	{

		for (k = 0; k < 3; k++)                      //绘制星
		{
			setfillcolor(BLACK);
			setlinecolor(BLACK);
			fillcircle(335 + x, 160 + y, 5);
			x += 180;

		}
		x = 0;
		y += 180;
	}
	j = 0;
	for (i = 0; i < 19; i++)                    //绘制数字
	{
	 
		    setbkmode(TRANSPARENT);
			settextcolor(BLACK);
		    settextstyle(15, 0, _T("黑体"));
			outtextxy(242 + j, 50, number[i]);
			outtextxy(210 , 60+j, bnumber[i]);
			j += 30;
	
	}
	settextcolor(RED);
	settextstyle(20, 0, _T("黑体"));
	outtextxy(50,400,"方向键控制,");
	outtextxy(50,420,"空格键落子");
	

}

int Playgame()
{
	int count=0;
	while (1)
	{
		if (KEY_DOWN(VK_UP)&&cur.y>70)
		{
			Drawcursor(cur, RGB(218, 144, 35));
			cur.y -= 30;
			Drawcursor(cur, RGB(0,0,0));


		}
		else if (KEY_DOWN(VK_DOWN)&&cur.y<610)
		{
			Drawcursor(cur, RGB(218, 144, 35));
			cur.y += 30;
			Drawcursor(cur, RGB(0, 0, 0));

		}
		else if (KEY_DOWN(VK_LEFT)&&cur.x>245)
		{
			Drawcursor(cur, RGB(218, 144, 35));
			cur.x -= 30;
			Drawcursor(cur, RGB(0, 0, 0));
		}
		else if(KEY_DOWN(VK_RIGHT)&&cur.x<785)
		{
			Drawcursor(cur, RGB(218, 144, 35));
			cur.x += 30;
			Drawcursor(cur, RGB(0, 0, 0));
		}
		else if(KEY_DOWN(VK_SPACE))
		{
        
		
			if((getpixel(cur.x-5,cur.y-5)!=RGB(0,0,0))&&(getpixel(cur.x-5,cur.y-5)!=RGB(255,255,255)))
			{
			    count++;
		    	if (count % 2 == 0)
				{
				  setfillcolor(BLACK);
				  solidcircle(cur.x, cur.y, 15);
				   if(isValid()==1)
				   {
					   setlinecolor(RED);
					   line(cur.x,cur.y,chess.x,chess.y);
					   Sleep(100);
					   MessageBox(NULL,"恭喜！黑棋获胜！","Game Over",MB_SYSTEMMODAL);
					   if(IDOK==1)
					   {
						   	_getch();
                        	closegraph(); 
							return 0;
					   }

				   }
		
				}
		    	else
				{
				  setfillcolor(WHITE);
			      solidcircle(cur.x, cur.y, 15);
				   if(isValid()==1)
				   {
					   setlinecolor(RED);
					   line(cur.x,cur.y,chess.x,chess.y);
					   Sleep(100);
					   MessageBox(NULL,"恭喜！白棋获胜！","Game Over",MB_SYSTEMMODAL);
					      if(IDOK==1)
					   {
						   	_getch();
                        	closegraph(); 
							return 0;
					   }

				   }
		
				}
		         
		
			}
			
		}
	    Showsteps(count);
		Sleep(100);
	
	}
}

void Drawcursor(postype cur, COLORREF cl)
{
	setlinecolor(cl);
	setlinestyle(PS_SOLID | PS_JOIN_BEVEL, 2);
	line(cur.x - 15, cur.y - 15, cur.x - 10, cur.y - 15);
	line(cur.x - 15, cur.y - 15, cur.x - 15, cur.y - 10);
	line(cur.x + 10, cur.y - 15, cur.x + 15, cur.y - 15);
	line(cur.x + 15, cur.y - 15, cur.x + 15, cur.y - 10);
	line(cur.x - 15, cur.y + 10, cur.x - 15, cur.y + 15);
	line(cur.x - 15, cur.y + 15, cur.x - 10, cur.y + 15);
	line(cur.x + 10, cur.y + 15, cur.x + 15, cur.y + 15);
	line(cur.x + 15, cur.y + 10, cur.x + 15, cur.y + 15);


}

void Showsteps(int count)
{
    setfillcolor(BLACK);
    solidrectangle(50,50,200,200);
	char str[20];
	settextstyle(20, 0, _T("黑体"));
    sprintf(str,"当前总步数：%d",count);
    settextcolor(RED);
	outtextxy(50,50,str); 

}




int isValid()
{
   for(int i=0;i<5;i++)
   {
	   if(getpixel(cur.x-5,cur.y-5)==getpixel(cur.x-5,cur.y-5-i*30))
	   {index++;}
	}
    if(index==5)
	{
		chess.x=cur.x;
		chess.y=cur.y-120;
		return 1;  
	}
	   index=0;
	    for( i=0;i<5;i++)
   {
	    if(getpixel(cur.x-5,cur.y-5)==getpixel(cur.x-5,cur.y-5+i*30))
	   {index++;}
	}
		if(index==5)
	   {
			chess.x=cur.x;
		    chess.y=cur.y+120;
			return 1;
		}
	   index=0;
	    for( i=0;i<5;i++)
   {
	    if(getpixel(cur.x-5,cur.y-5)==getpixel(cur.x-5-i*30,cur.y-5))
	   {index++;}
	   	}
		 if(index==5)
	   {
			 chess.x=cur.x-120;
		     chess.y=cur.y;
			 return 1;
		 }
	   index=0;
	    for( i=0;i<5;i++)
   {
	    if(getpixel(cur.x-5,cur.y-5)==getpixel(cur.x-5+i*30,cur.y-5-i*30))
	   {index++;}
	   	}
		  if(index==5)
	   {

			  chess.x=cur.x+120;
		      chess.y=cur.y;
			  return 1;
		  
		}
	   index=0;
	    for( i=0;i<5;i++)
   {
	    if(getpixel(cur.x-5,cur.y-5)==getpixel(cur.x-5-i*30,cur.y-5-i*30))
	   {index++;}
	   	}
	   if(index==5)
	   {
		   chess.x=cur.x-120;
		   chess.y=cur.y-120;
		   return 1;
	   }
	   index=0;
	    for( i=0;i<5;i++)
   {
	    if(getpixel(cur.x-5,cur.y-5)==getpixel(cur.x-5+i*30,cur.y-5-i*30))
	   {index++;}
	   	}
		   if(index==5)
	   {
			   chess.x=cur.x+120;
	          chess.y=cur.y-120;
			   return 1;
		   }
	   index=0;
	    for( i=0;i<5;i++)
   {
	    if(getpixel(cur.x-5,cur.y-5)==getpixel(cur.x-5+i*30,cur.y-5+i*30))
	   {index++;}
	}
	   if(index==5)
	   {
		   chess.x=cur.x+120;
	       chess.y=cur.y+120;
		   return 1;
	   }
	   index=0;
	    for( i=0;i<5;i++)
   {
	    if(getpixel(cur.x-5,cur.y-5)==getpixel(cur.x-5-i*30,cur.y-5+i*30))
	   {index++;}
   }
		 if(index==5)
	   {
			 chess.x=cur.x-120;
		     chess.y=cur.y+120;
			 return 1;
		 }
	   index=0;

}

 void Gameover()
 {
	cleardevice();
	settextcolor(RGB(255,0,0));
	settextstyle(80, 0, _T("黑体"));
	outtextxy(300, 330, "Game Over");  
 }
