# 8-深度优先
#include <iostream>
#include <vector>
using namespace std;

struct Octal
{
	int arr[4][4]; // 存储八数码
	int parent; // 父亲的值
	int current; // 当前的值
	int depth; // 当前深度
};

int to_number(int [4][4]);
bool up(int [4][4]);
bool down(int [4][4]);
bool left(int [4][4]);
bool right(int [4][4]);

int main()
{
	int arr[4][4] = {0}; // 存储当前八数码
	vector<Octal> open; // open表
	vector<Octal> closed; // closed表
	int depth; // 深度
	Octal octal; // 八数码

	cout << "请输入八数码数据，按照从左到右，从上至下的顺序，空格用0代替：" << endl;
	for (int i = 1; i <= 3; i++)
	{
		for (int j = 1; j <= 3; j++)
		{
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

	while (!open.empty())
	{
		temp.current = open[open.size()-1].current;
		temp.parent = open[open.size()-1].parent;
		temp.depth = open[open.size()-1].depth;
		for (int i = 1; i <= 3; i++)
			for (int j = 1; j <= 3; j++)
				temp.arr[i][j] = open[open.size()-1].arr[i][j];
			
		open.pop_back();
		closed.push_back(temp);
		if (temp.current == goal)
		{
			flag = true;
			break;
		}
		if (temp.depth >= depth)
		{
			continue;
		}
		
		Octal produce[4]; // 四个可能生成的八数码数组
		for (int i = 0; i < 4; i++)
		{
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

		
		if (up(produce[0].arr))
		{
			produce[0].current = to_number(produce[0].arr);
			for (int i = 0; i < open.size(); i++)
				if (open[i].current == produce[0].current)
				{
					flagProduce[0][0] = false;
					break;
				}
				
			if (flagProduce[0][0])
			{
				for (int i = 0; i < closed.size(); i++)
					if (closed[i].current == produce[0].current)
					{
						flagProduce[0][1] = false;
						break;
					}
				if (flagProduce[0][1])
					open.push_back(produce[0]);
			}
		}
		if (down(produce[1].arr))
		{
			produce[1].current = to_number(produce[1].arr);
			for (int i = 0; i < open.size(); i++)
				if (open[i].current == produce[1].current)
				{
					flagProduce[1][0] = false;
					break;
				}

			if (flagProduce[1][0])
			{
				for (int i = 0; i < closed.size(); i++)
					if (closed[i].current == produce[1].current)
					{
						flagProduce[1][1] = false;
						break;
					}
				if (flagProduce[1][1])
					open.push_back(produce[1]);
			}
		}
		
		if (left(produce[2].arr))
		{
			produce[2].current = to_number(produce[2].arr);
			for (int i = 0; i < open.size(); i++)
				if (open[i].current == produce[2].current)
				{
					flagProduce[2][0] = false;
					break;
				}

			if (flagProduce[2][0])
			{
				for (int i = 0; i < closed.size(); i++)
					if (closed[i].current == produce[2].current)
					{
						flagProduce[2][1] = false;
						break;
					}
				if (flagProduce[2][1])
					open.push_back(produce[2]);
			}
		}
		
		if (right(produce[3].arr))
		{
			produce[3].current = to_number(produce[3].arr);
			for (int i = 0; i < open.size(); i++)
				if (open[i].current == produce[3].current)
				{
					flagProduce[3][0] = false;
					break;
				}

			if (flagProduce[3][0])
			{
				for (int i = 0; i < closed.size(); i++)
					if (closed[i].current == produce[3].current)
					{
						flagProduce[3][1] = false;
						break;
					}
				if (flagProduce[3][1])
					open.push_back(produce[3]);
			}
 		}
	}

	if (flag)
	{
		vector<Octal> answer;
		cout << "找到一个解！" << endl;
		answer.push_back(temp);
		int parent = temp.parent;

		while(parent != 0)
		{
			for (int j = 0; j < closed.size(); j++)
			{
				if (closed[j].current == parent)
				{
  					temp.current = closed[j].current;
					temp.depth = closed[j].depth;
					temp.parent = closed[j].parent;
					for (int k = 1; k <= 3; k++)
					{
						for (int m = 1; m <= 3; m++)
						{
							temp.arr[k][m] = closed[j].arr[k][m];
						}
					}
					answer.push_back(temp);
					parent = closed[j].parent;
					break;
				}
			}
		}
		for (int i = answer.size()-1; i >= 0; i--)
		{
			cout << "第" << answer.size()-i-1 << "次：" << endl;
			for (int j = 1; j <= 3; j++)
			{
				for (int k = 1; k <= 3; k++)
				{
					cout << answer[i].arr[j][k] << " ";
				}
				cout << endl;
			}
		}
	}
	else
	{
		cout << "在深度限制内的所有结果搜索完毕，没有答案！" << endl;
	}

	return 0;
}

int to_number(int arr[4][4])
{
	// 将八数码矩阵中的数据转换为数字，方便存储和比较
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

bool up(int arr[4][4])
{
	// 空格向上
	int i, j; // 空格的位置为(i, j)
	bool flag = false;
	for (i = 1; i <= 3; i++)
	{
		for (j = 1; j <= 3; j++)
			if (arr[i][j] == 0)
			{
				flag = true;
				break;
			}
		if (flag)
			break;
	}
	if (i == 1)
		return false; // 此时无法向上
	else
	{
		arr[i][j] = arr[i-1][j];
		arr[i-1][j] = 0;
	}
	return true;
}

bool down(int arr[4][4])
{
	// 空格向下
	int i, j; // 空格的位置为(i, j)
	bool flag = false;
	for (i = 1; i <= 3; i++)
	{
		for (j = 1; j <= 3; j++)
			if (arr[i][j] == 0)
			{
				flag = true;
				break;
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

bool left(int arr[4][4])
{
	// 空格向左
	int i, j; // 空格的位置为(i, j)
	bool flag = false;
	for (i = 1; i <= 3; i++)
	{
		for (j = 1; j <= 3; j++)
			if (arr[i][j] == 0)
			{
				flag = true;
				break;
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
	for (i = 1; i <= 3; i++)
	{
		for (j = 1; j <= 3; j++)
			if (arr[i][j] == 0)
			{
				flag = true;
				break;
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


# 8-广度优先

#include <iostream>
#include <queue>
#include <stack>
#include <vector>
#include <algorithm>
#include <memory.h>

using namespace std;

// 八数码状态
typedef struct _Status{
    int status[3][3];
    _Status *parent;
    _Status *next;
}Status;

// AStar排序依据
bool decComparator(const Status &s1, const Status &s2){
    int gn1 = 0, gn2 = 0;
    int dn1 = 0, dn2 = 0;
    const Status *ptr1 = &s1;
    const Status *ptr2 = &s2;
    int status[3][3] = {1,2,3,8,0,4,7,6,5};
    while(ptr1 != NULL){
        gn1 += 1;
        ptr1 = ptr1->parent;
    }
    while(ptr2 != NULL){
        gn2 += 1;
        ptr2 = ptr2->parent;
    }
    for(int i = 0; i < 3; i++){
        for(int j = 0; j < 3; j++){
            if(s1.status[i][j] != status[i][j]){
                dn1 += 1;
            }
            if(s2.status[i][j] != status[i][j]){
                dn2 += 1;
            }
        }
    }
    return (gn1+dn1) > (gn2+dn2);
}

// 八数码搜索
class EightPuzzle{
private:
    unsigned char allHash[362880];
    Status root;
    Status goal;
private:
    int nextNumber;
    Status next[4];
public:
    EightPuzzle(Status *root, Status *goal){
        memcpy(&this->root.status, &root->status, sizeof(int)*9);
        this->root.parent = NULL;
        this->root.next = NULL;
        memcpy(&this->goal.status, &goal->status, sizeof(int)*9);
        this->goal.parent = NULL;
        this->goal.next = NULL;
    }
private:
    // 判断是否是目标状态
    inline int IsGoal(Status *tmp){
        return memcmp(&tmp->status, &goal.status, sizeof(int)*9);
    }
    // 下一个可行的状态
    int NextStatus(Status *tmp){
        nextNumber = 0;
        int posi, posj;
        for(int i = 0; i < 9; i++){
            posi = i/3, posj = i - i/3*3;
            if(tmp->status[posi][posj] == 0){
                break;
            }
        }
        if(posi-1 >= 0){
            Status left = *tmp;
            left.status[posi][posj] = left.status[posi-1][posj];
            left.status[posi-1][posj] = 0;
            if(allHash[Cantor(left.status)] == 0){
                next[nextNumber] = left;
                next[nextNumber].parent = tmp;
                nextNumber++;
            }
        }
        if(posi+1 <= 2){
            Status right = *tmp;
            right.status[posi][posj] = right.status[posi+1][posj];
            right.status[posi+1][posj] = 0;
            if(allHash[Cantor(right.status)] == 0){
                next[nextNumber] = right;
                next[nextNumber].parent = tmp;
                nextNumber++;
            }
        }
        if(posj-1 >= 0){
            Status up = *tmp;
            up.status[posi][posj] = up.status[posi][posj-1];
            up.status[posi][posj-1] = 0;
            if(allHash[Cantor(up.status)] == 0){
                next[nextNumber] = up;
                next[nextNumber].parent = tmp;
                nextNumber++;
            }
        }
        if(posj+1 <= 2){
            Status down = *tmp;
            down.status[posi][posj] = down.status[posi][posj+1];
            down.status[posi][posj+1] = 0;
            if(allHash[Cantor(down.status)] == 0){
                next[nextNumber] = down;
                next[nextNumber].parent = tmp;
                nextNumber++;
            }
        }
        return nextNumber;
    }
    // 康托展开
    int Cantor(int arr[][3]){
        int fac[10] = {1,1,2,6,24,120,720,5040,40320,362880};
        int index = 0;
        for(int i = 7; i >= 0; i--){
            int irow = i/3, icol = i - i/3*3;
            int count = 0;
            for(int j = 8; j > i; j--){
                int jrow = j/3, jcol = j - j/3*3;
                if(arr[jrow][jcol] < arr[irow][icol]){
                    count++;
                }
            }
            index += (count*fac[8-i]);
        }
        return index;
    }
public:
    // 广度优先搜索
    int BFS(){
        int step = 0;
        memset(allHash, 0, 362880);
        queue<Status> openTable;
        Status *closeTable = new Status;;
        Status *current = closeTable;
        Status *tmp;
        openTable.push(root);
        allHash[Cantor(root.status)] == 1;
        while(!openTable.empty()){
            tmp = new Status;
            *tmp = openTable.front();
            openTable.pop();
            step++;
            current->next = tmp;
            current = current->next;
            if(IsGoal(tmp) == 0){
                PrintPath(tmp);
                freeCloseTable(closeTable);
                return step;
            }
            int nextNumber = NextStatus(tmp);
            if(nextNumber == 0){
                continue;
            }
            for(int i = 0; i < nextNumber; i++){
                openTable.push(next[i]);
                allHash[Cantor(next[i].status)] == 1;
            }
        }
        cout << "BFS failed." << endl;
        freeCloseTable(closeTable);
        return -1;
    }
private:
    // 打印路径
    void PrintPath(Status *head){
        if(head == NULL){
            return;
        }
        else{
            PrintPath(head->parent);
            for(int i = 0; i < 3; i++){
                for(int j = 0; j < 3; j++){
                    cout << head->status[i][j];
                }
                cout << endl;
            }
            cout <<endl;
        }
    }
    // 释放close表
    void freeCloseTable(Status *closeTable){
        Status *current;
        while(closeTable != NULL){
            current = closeTable->next;
            free(closeTable);
            closeTable = current;
        }
    }
};

int main()
{
    Status init = {2,8,3,1,6,4,7,0,5,0,NULL};
    Status goal = {1,2,3,8,0,4,7,6,5,0,NULL};
    EightPuzzle ep = EightPuzzle(&init, &goal);
    cout << "BFS********\n" << endl;
    cout << "step: " << ep.BFS() << endl;
    cout << "***********\n" << endl;
    return 0;
}
