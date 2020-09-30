# 8-A star算法
#include"stdio.h"  

/*显示当前待调整数码矩阵*/  
void show(int begin[3][3]){  
    for(int i = 0; i < 3; i++){  
        for(int j = 0; j < 3; j++){  
            printf("%d ", begin[i][j]);
		}
        printf("\n");  
    }  
    printf("\n");  
}  
  
/*交换数码中两个数位置*/  
void exchange(int begin[3][3], int row1, int column1, int row2, int column2){  
    int temp;  
    temp = begin[row1][column1] ;  
    begin[row1][column1] = begin[row2][column2];  
    begin[row2][column2] = temp;  
}  
  
/*判断待调整的数码与最终数码相比正确位置数码的个数*/  
int judge(int begin[3][3], int end[3][3]){  
    int count=0;           
    for(int i = 0; i < 3; i++){   
        for(int j = 0; j < 3; j++){  
            if(begin[i][j] == end[i][j] && end[i][j] != 0) 
                count++; 
		}
	}   
    return count;           
}  
   
/* 将待调整数码从开始位置移动到终止位置，并将其过程输出*/  
int move(int begin[3][3], int end[3][3]  
           , int right, int count, int count_1[50][3][3]  
           , int biaoji, int row, int column) //biaoji存储上一轮移动的反方向代号  
{   
    int temp1;  
    show(begin);   //显示数组矩阵  
    if(count >= 20)  
        return 0;  
    int node;  //,node为标记  
    int temp;               //存储当前待调整数码正确的个数  
    for(int q=0; q<count; q++){ //检查交换后的end[][]图形是否先前已经遍历过了 
        node = 1;  
        for(int w=0; w<3 && node; w++){  
            for(int r=0; r<3 && node; r++){  
                if(count_1[q][w][r] != begin[w][r])  
                    node = 0;
			}
		}
        if(node == 1){ //如果先前遍历过，返回0    
            return 0;  
        }  
    }  
    for(int i = 0; i < 3; i++){     
        for(int j = 0; j < 3; j++){  
            count_1[count][i][j] = begin[i][j];
		}
	}
    if(right == 3 * 3 - 1)  //如果待调整数码与最终数码完全相同时，返回1  
        return 1;  
    if(row > 0 && biaoji != 0){             //存储0的位置不是在第一行  
        exchange(begin, row - 1, column, row , column);  //将0与其上面的元素交换存储位置  
        temp = judge(begin, end);  
        if(temp < right)   //如果交换后正确数码的个数不大于原来正确数码的个数  
            exchange(begin, row - 1, column, row , column); //再将其交换回来         
        else if(temp >= right) {          //如果交换后正确数码的个数大于或等于原来正确数码的个数  
            temp1 = move(begin, end, temp, count+1, count_1, 2, row-1, column);  
            if( temp1 == 1)  //进行下一步的移动  
                return 1;  
            exchange(begin, row - 1, column, row , column); //再将其交换回来  
        }  
    }  
    if(column > 0 && biaoji != 1){  
        exchange(begin, row, column - 1, row , column); //将0与其左边的元素交换存储位置  
        temp = judge(begin, end);         
        if(temp < right)     
           exchange(begin, row, column - 1, row , column);            
        else if(temp >= right){  
            temp1 = move(begin, end, temp, count+1, count_1 ,3, row, column - 1);  
            if(temp1 == 1)    
                 return 1;  
            exchange(begin, row, column - 1, row , column);  
        }  
    }  
    if(row < 3-1 && biaoji != 2){  
        exchange(begin, row + 1, column, row , column); //将0与其下面的元素交换存储位置  
        temp = judge(begin, end);     
        if(temp < right)   
            exchange(begin, row + 1, column, row , column);  
        else if(temp >= right){  
            temp1 =move(begin, end, temp, count+1, count_1, 0, row+1, column);  
           if(temp1 == 1)    
              return 1;  
           exchange(begin, row + 1, column, row , column);  
        }  
    }  
    if(column < 3-1 && biaoji != 3){  
        exchange(begin, row, column + 1, row , column); //将0与其右边的元素交换存储位置  
        temp = judge(begin, end);     
        if(temp < right)     
            exchange(begin, row, column + 1, row , column);       
        else if(temp >= right){  
            temp1 = move(begin, end, temp, count+1, count_1, 1, row, column+1);  
            if(temp1 == 1)    
               return 1;  
            exchange(begin, row, column + 1, row , column);   
        }  
    }  
    return 0;   //移动失败，返回0  
}  
  
/*输入初始数组*/  
void input(int begin[3][3],int blank[]){  
    int temp, node, zero = 0;  
    for (int i = 0; i < 3; i++){  
        for(int j = 0; j < 3; j++){  
            node = 1;  
            printf("请输入第%d行，第%d列的元素的值：", i+1, j+1);  
            scanf("%d", &temp);  
            for (int q = 0; q <= i && node == 1; q++){  //当输入的值有重复的，提示重新输入  
                for (int w = 0; w < j; w++){  
                    if(temp == begin[q][w]){  
                        printf("输入重复，请重新输入\n");  
                        node = 0;  
                        j--;  
                        break;  
                    }
				}
			}
            if(temp < 0 || temp > 3*3-1){   //当输入的值不是在数码的区间范围内时，提示重新输入  
                printf("请输入从%d到%d的数\n", zero, 3*3-1);  
                node = 0;  
                j--;  
            }  
            if(node == 1){     //如果输入满足条件    
                if(temp == 0){ //如果输入的值为零，由blank[0]记录行号，blank[1]记录列号    
                    blank[0] = i;  
                    blank[1] = j;  
                }  
                begin[i][j] = temp; 
            }  
        }  
	}
}  
  
int main()  
{  
    int Count = 0, Count_[50][3][3];  
    int row;       
    int column;  
    int begin[3][3], blank[2],count=1;    
    int end[3][3] = {1, 2, 4, 0, 6, 3, 7, 8, 5};  //给最终状态的数码矩阵赋值  
    printf ("-------%d数码游戏开始！--------\n", 3);  
    input(begin, blank);  
    row = blank[0];  
    column = blank[1];  
    if(move (begin, end,judge(begin,end),Count,Count_,4,row,column) == 0)    
       printf("\n此8数码的问题可能无解！");  
    else   
       show(begin);  
    return 0;  
}  




# 8-深度优先
#include <iostream>
#include <vector>
using namespace std;

struct Octal
{
	int arr[4][4]; 
	int parent; 
	int current; 
	int depth; 
};

int to_number(int arr[4][4]){
	// 从左到右，从上至下，按照从1到9递增，1为亿位，……，9为个位
	int index = 100000000;               
	long long result = 0;
	for (int i = 1; i <= 3; i++)
	{
		for (int j = 1; j <= 3; j++)
		{
			result += arr[i][j] * index;
			index /= 10;
		}
	}
	return result;
}

bool up(int arr[4][4]){
	// 空格向上
	int i, j; // 空格的位置为(i, j)
	bool flag = false;
	for (i = 1; i <= 3; i++){
		for (j = 1; j <= 3; j++){
			if (arr[i][j] == 0){
				flag = true;
				break;
			}
		}
		if (flag)
			break;
	}
	if (i == 1)
		return false; 
	else{
		arr[i][j] = arr[i-1][j];
		arr[i-1][j] = 0;
	}
	return true;
}

bool down(int arr[4][4]){
	// 空格向下
	int i, j; // 空格的位置为(i, j)
	bool flag = false;
	for (i = 1; i <= 3; i++){
		for (j = 1; j <= 3; j++){
			if (arr[i][j] == 0){
				flag = true;
				break;
			}
		}
		if (flag)
			break;
	}
    if (i == 3)
		return false; // 此时无法向下
	else
	{
		arr[i][j] = arr[i+1][j];
		arr[i+1][j] = 0;
	}
	return true;
}

bool left(int arr[4][4]){
	// 空格向左
	int i, j; // 空格的位置为(i, j)
	bool flag = false;
	for (i = 1; i <= 3; i++){
		for (j = 1; j <= 3; j++){
			if (arr[i][j] == 0){
				flag = true;
				break;
			}
		}
		if (flag)
			break;
	}
	if (j == 1)
		return false; // 此时无法向左
	else
	{
		arr[i][j] = arr[i][j-1];
		arr[i][j-1] = 0;
	}
	return true;
}

bool right(int arr[4][4])
{
	// 空格向右
	int i, j; // 空格的位置为(i, j)
	bool flag = false;
	for (i = 1; i <= 3; i++){
		for (j = 1; j <= 3; j++){
			if (arr[i][j] == 0){
				flag = true;
				break;
			}
		}
		if (flag)
			break;
	}
	if (j == 3)
		return false; // 此时无法向右
	else
	{
		arr[i][j] = arr[i][j+1];
		arr[i][j+1] = 0;
	}
	return true;
}

int main()
{
	int arr[4][4] = {0}; // 存储当前八数码
	vector<Octal> open; // open表
	vector<Octal> closed; // closed表
	int depth; // 深度
	Octal octal; // 八数码
	cout << "请输入八数码数据，按照从左到右，从上至下的顺序，空格用0代替：" << endl;
	for (int i = 1; i <= 3; i++){
		for (int j = 1; j <= 3; j++){
			cin >> arr[i][j];
			octal.arr[i][j] = arr[i][j];
		}
	}
	cout << "请输入深度：" << endl;
	cin >> depth;
	cout << "===开始运算===" << endl;

	octal.parent = 0;
	octal.current = to_number(arr);
	octal.depth = 0;
	open.push_back(octal);

	int goal = 123804765; // 最终的八数码值
	bool flag = false; // 成功解决为true，失败为false
	Octal temp;
	int count = 1;

	while (!open.empty()){
		temp.current = open[open.size()-1].current;
		temp.parent = open[open.size()-1].parent;
		temp.depth = open[open.size()-1].depth;
		for (int i = 1; i <= 3; i++)
			for (int j = 1; j <= 3; j++)
				temp.arr[i][j] = open[open.size()-1].arr[i][j];
			
		open.pop_back();
		closed.push_back(temp);
		if (temp.current == goal){
			flag = true;
			break;
		}
		if (temp.depth >= depth){
			continue;
		}
		
		Octal produce[4]; // 四个可能生成的八数码数组
		for (int i = 0; i < 4; i++){
			produce[i].parent = temp.current;
			for (int j = 1; j <= 3; j++)
				for (int k = 1; k <= 3; k++)
					produce[i].arr[j][k] = temp.arr[j][k];
			produce[i].depth = temp.depth + 1;
		}
		
		bool flagProduce[4][2]; // 四个八数码对应的标志，0为open中是否含有，1位closed中是否含有
		for (int i = 0; i < 4; i++)
			for (int j = 0; j < 2; j++)
				flagProduce[i][j] = true;

		
		if (up(produce[0].arr)){
			produce[0].current = to_number(produce[0].arr);
			for (int i = 0; i < open.size(); i++)
				if (open[i].current == produce[0].current){
					flagProduce[0][0] = false;
					break;
				}
				
			if (flagProduce[0][0]){
				for (int i = 0; i < closed.size(); i++)
					if (closed[i].current == produce[0].current){
						flagProduce[0][1] = false;
						break;
					}
				if (flagProduce[0][1])
					open.push_back(produce[0]);
			}
		}

		if (down(produce[1].arr)){
			produce[1].current = to_number(produce[1].arr);
			for (int i = 0; i < open.size(); i++)
				if (open[i].current == produce[1].current){
					flagProduce[1][0] = false;
					break;
				}

			if (flagProduce[1][0]){
				for (int i = 0; i < closed.size(); i++)
					if (closed[i].current == produce[1].current){
						flagProduce[1][1] = false;
						break;
					}
				if (flagProduce[1][1])
					open.push_back(produce[1]);
			}
		}
		
		if (left(produce[2].arr)){
			produce[2].current = to_number(produce[2].arr);
			for (int i = 0; i < open.size(); i++)
				if (open[i].current == produce[2].current){
					flagProduce[2][0] = false;
					break;
				}

			if (flagProduce[2][0]){
				for (int i = 0; i < closed.size(); i++)
					if (closed[i].current == produce[2].current){
						flagProduce[2][1] = false;
						break;
					}
				if (flagProduce[2][1])
					open.push_back(produce[2]);
			}
		}
		
		if (right(produce[3].arr)){
			produce[3].current = to_number(produce[3].arr);
			for (int i = 0; i < open.size(); i++)
				if (open[i].current == produce[3].current){
					flagProduce[3][0] = false;
					break;
				}

			if (flagProduce[3][0]){
				for (int i = 0; i < closed.size(); i++)
					if (closed[i].current == produce[3].current){
						flagProduce[3][1] = false;
						break;
					}
				if (flagProduce[3][1])
					open.push_back(produce[3]);
			}
 		}
	}

	if (flag){
		vector<Octal> answer;
		cout << "找到一个解！" << endl;
		answer.push_back(temp);
		int parent = temp.parent;
		while(parent != 0){
			for (int j = 0; j < closed.size(); j++){
				if (closed[j].current == parent){
  					temp.current = closed[j].current;
					temp.depth = closed[j].depth;
					temp.parent = closed[j].parent;
					for (int k = 1; k <= 3; k++){
						for (int m = 1; m <= 3; m++){
							temp.arr[k][m] = closed[j].arr[k][m];
						}
					}
					answer.push_back(temp);
					parent = closed[j].parent;
					break;
				}
			}
		}

		for (int i = answer.size()-1; i >= 0; i--){
			cout << "第" << answer.size()-i-1 << "次：" << endl;
			for (int j = 1; j <= 3; j++){
				for (int k = 1; k <= 3; k++){
					cout << answer[i].arr[j][k] << " ";
				}
				cout << endl;
			}
		}
	}
	else{
		cout << "在深度限制内的所有结果搜索完毕，没有答案！" << endl;
	}
	return 0;
}



# 8-广度优先
#include <iostream>
#include <string>
#include <cstring>
#include <cmath>
#include <vector>
#include <queue>
#include <set>
using namespace std;
#define N 9

int jc[N+1]={1,1,2,6,24,120,720,5040,40320,362880};//0-9的阶乘

typedef struct data{
    int arr[N];
    int hash;//存储某一格局的哈希
    int pos;//0当前位置
    int step;
}Node;
 
int dir[4][2]={
    {0,1},
    {1,0},
    {0,-1},
    {-1,0}
};
 
int cantor(int arr[N]){
    int i,j;
    int sum=0;
    for( i=0;i<N;i++){
        int nmin=0;
        for(j=i+1;j<N;j++){
            if(arr[i]>arr[j])
                nmin++;
        }
        sum+=(nmin*jc[N-i-1]);
    }
    return sum;
}
 
void swap(int *arr,int i,int j){
    int t=arr[i];
    arr[i]=arr[j];
    arr[j]=t;
}
 
void printArray(int * arr){
    int i,j;
    for (i=0;i<N;i++){
        if(i%3==0)
            cout<<"\n";
        cout<<arr[i]<<" ";
    }
    cout<<endl;
}

void copyArray(int src[N],int target[N]){
    int i;
    for(i=0;i<N;i++){
        target[i]=src[i];
    }
}
 
int bfs(int arr[N],int sHash,int tHash){
    if(sHash==tHash)
        return 0;
    int i,j;
    queue<Node> q;
    set<int> setHash;
    Node now,next;
    copyArray(arr,now.arr);
    int pos=0;
    for (i=0;i<N;i++){
        if(arr[i]==0)
            break;
        pos++;
    }
    now.hash=sHash;
    now.step=0;
    next.step=0;
    now.pos=pos;
    q.push(now);
    setHash.insert(now.hash);
    while(!q.empty()){
        now=q.front();
        q.pop();
        for (i=0;i<4;i++){
            int offsetX=0,offsetY=0;
            offsetX=(now.pos%3+dir[i][0]);
            offsetY=(now.pos/3+dir[i][1]);
            if(offsetX>=0&&offsetX<3&&offsetY<3&&offsetY>=0){
                copyArray(now.arr,next.arr);
                next.step=now.step;
                next.step++;
                swap(next.arr,now.pos,offsetY*3+offsetX);
                next.hash=cantor(next.arr);
                next.pos=(offsetY*3+offsetX);
                int begin=setHash.size();
                setHash.insert(next.hash);
                int end=setHash.size();
                if(next.hash==tHash){
                    return next.step;
                }
                if(end>begin){
                    q.push(next);
                }
 
            }
        }
 
    }
    return -1;
}
 
int inversion(int arr[N]){
    int sum=0;
    for(int i=0;i<N;++i){
        for(int j=i+1;j<N;++j){
            if(arr[j]<arr[i]&&arr[j]!=0){
                sum++;
            }
        }
    }
    return sum;
}

int main(int argc, char **argv)
{
    int i,j;
    string s="123456780";
    string t="123456078";
    int is[N],it[N];//源int数组和目标int数组
    for (i=0;i<9;i++){
        if (s.at(i)>='0'&&s.at(i)<='8'){
            is[i]=s.at(i)-'0';
        }
		else{
            is[i]=0;
        }
        if (t.at(i)>='0'&&t.at(i)<='8'){
            it[i]=t.at(i)-'0';
        }
		else{
            it[i]=0;
        }
    }
    int sHash,tHash;
    sHash=cantor(is);
    tHash=cantor(it);
    int inver1=inversion(is);
    int inver2=inversion(it);
    if((inver1+inver2)%2==0){
        int step=bfs(is,sHash,tHash);
        cout<<step<<endl;
    }
    else{
        cout<<"无法从初始状态到达目标状态"<<endl;
    }
    return 0;
}

