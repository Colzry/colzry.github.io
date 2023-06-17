---
title: "DFS和BFS题型"
description: "DFS和BFS题型"
keywords: "算法"

date: 2023-06-16T16:41:30+08:00
lastmod: 2023-06-16T16:41:30+08:00

categories:
  - 算法
tags:
  - 算法
---

## 剪格子

> 题目描述


问题描述
如下图所示，3 x 3 的格子中填写了一些整数。
+--*--+--+
|10* 1|52|
+----+
|20|30* 1|
***--+
| 1| 2| 3|
+--+--+--+
我们沿着图中的星号线剪开，得到两个部分，每个部分的数字和都是60。
本题的要求就是请你编程判定：对给定的m x n 的格子中的整数，是否可以分割为两个部分，使得这两个区域的数字和相等。
如果存在多种解答，请输出包含左上角格子的那个区域包含的格子的最小数目。
如果无法分割，则输出 0。

> 输入


程序先读入两个整数 m n 用空格分割 (m,n< 10)。
表示表格的宽度和高度。
接下来是n行，每行m个正整数，用空格分开。每个整数不大于10000。

> 输出


输出一个整数，表示在所有解中，包含左上角的分割区可能包含的最小的格子数目。

> 样例输入


```
3 3
10 1 52
20 30 1
1 2 3
```

> 样例输出


```
3
```

```java
public class 剪格子 {
	private static int[][] lattice; // 格子
	private static boolean[][] isvisited; // 是否被访问
	private static final int[][] direction = {{0, 1}, {1, 0}, {0, -1}, {-1, 0}}; // 行走的策略，按顺时针
	private static int sum = 0;
	private static int count = 100; // 最小格子数，先定义100表示最大
	
	public static void main(String[] args) {
		try(Scanner sc = new Scanner(System.in)) {
			int m = sc.nextInt();
			int n = sc.nextInt();
			lattice = new int[n][m];
			isvisited = new boolean[n][m];
			for (int i = 0; i < n; i++)
				for (int j = 0; j < m; j++) {
					lattice[i][j] = sc.nextInt();
					sum += lattice[i][j];
				}
			if (sum % 2 == 0) { // 如果和不是偶数就不能剪
				dfs(0, 0, lattice[0][0], 1);
				if (count != 100) {
					System.out.println(count);
				} else {
					System.out.println(0);
				}
			} else {
				System.out.println(0);
			}
		}
	}
	
	public static void dfs(int x, int y, int curSum, int curCount) {
		if (curSum == sum / 2 && curCount < count) {
			count = curCount;
			return;
		}
		
		for (int i = 0; i < 4; i++) {
			int dx = x + direction[i][0];
			int dy = y + direction[i][1];
			
			if ( (dx >= 0 && dx < lattice.length) &&
					(dy >= 0 && dy < lattice[0].length) && !isvisited[dx][dy]) {
				isvisited[dx][dy] = true;
				curSum += lattice[dx][dy]; 
				// 若不要49行和52行可把下面一行改为dfs(dx, dy, curSum + isvisited[dx][dy], curCount + 1);
				dfs(dx, dy, curSum, curCount + 1);
				isvisited[dx][dy] = false; // 回溯 (表示你这方法不行)
				curSum -= lattice[dx][dy]; // 回溯 (把原来加的给减了)
			}
		}
	}

}
```

## 九宫重排

> 题目描述


如下面第一个图的九宫格中，放着 1~8 的数字卡片，还有一个格子空着。与空格子相邻的格子中的卡片可以移动到空格中。经过若干次移动，可以形成第二个图所示的局面。

![](https://picture-czy.oss-cn-beijing.aliyuncs.com/img/20210520201058.png#crop=0&crop=0&crop=1&crop=1&id=vyDup&originHeight=203&originWidth=415&originalType=binary&ratio=1&rotation=0&showTitle=false&status=done&style=none&title=)

我们把第一个图的局面记为：12345678.
把第二个图的局面记为：123.46758
显然是按从上到下，从左到右的顺序记录数字，空格记为句点。
本题目的任务是已知九宫的初态和终态，求最少经过多少步的移动可以到达。如果无论多少步都无法到达，则输出-1。

> 输入


输入第一行包含九宫的初态，第二行包含九宫的终态。

> 输出


输出最少的步数，如果不存在方案，则输出-1。

> 样例输入


```
12345678. 
123.46758
```

> 样例输出


```
3
```

```java
public class 九宫重排 {

	public static void main(String[] args) {
		// 用来保存初始字符串
		Queue<String> queue = new LinkedList<String>();
		// 用来做字符串去重
		HashMap<String, Integer> map = new HashMap<String, Integer>();
		int step = 0;
		try(Scanner sc = new Scanner(System.in)) {
			String start = sc.nextLine();
			String end = sc.nextLine();
			
			queue.add(start);
			map.put(start, step);
			
			if (end.equals(start)) {
				System.out.println(0);
				return;
			}
			
			int[] dis = {-3, 3, -1, 1};
			while (!queue.isEmpty()) {
					String cur = queue.poll();
					int index = cur.indexOf('.');
					for (int i = 0; i < 4; i++) {
						if ((index % 3 == 0 && i == 2) || (index % 3 == 2 && i == 3))
							continue;
						int curDis = index + dis[i];
						if ((curDis >= 0 && curDis <= 8)) {
							String newStr = swap(cur.toCharArray(), index, curDis);
							if (end.equals(newStr)) {
								System.out.println(map.get(cur) + 1);
								return;
							}
							if (!map.containsKey(newStr)) {
								queue.add(newStr);
								map.put(newStr, map.get(cur) + 1);
							}
						}
					}
				}
			System.out.println(-1);
		}
		
	}

	public static String swap(char[] target, int x, int y) {
		char temp = target[x];
		target[x] = target[y];
		target[y] = temp;
		return String.valueOf(target);
	}

}
```

## 误落迷宫

> 题目描述


希里掉入三夫人的迷宫了，这是一个真正的迷宫，蜿蜒复杂，希里的目的只有一个，那就是找到三夫人，到达出口。
给定一个二维矩阵表示迷宫，你需要做到的是，从迷宫的出口处找到三夫人，再从三夫人处走向终点。
其中'S'表示起始点，'T'表示三夫人处，'E'表示迷宫重点。
'1'表示墙壁，意思是不可达位置，'0'表示通路，意思是可以移动的位置
每次移动可以从上下左右四个方向进行移动。
你需要告诉希里，最短需要走多少步能够完成上述目标。

> 输入


第一行一个n,m分别表示迷宫的大小。
接下来n行每行m字符表示迷宫
2 < n,m <= 50

> 输出


达成目标的最小步数

> 样例输入


```
8 8
1S111111
10000001
11101101
1T001001
10011001
10000001
10000001
111E1111
```

> 样例输出


```
13
```

```java
import java.util.Arrays;
import java.util.LinkedList;
import java.util.Scanner;
public class 误落迷宫 {
    static char[][] arr;//地图
    static int n, m;//行数，列数
    static boolean[][] isVsted;//标记站点是否走过  true为走过 false为没有走过
    public static void main(String[] args) {
        Scanner sc = new Scanner(System.in);
        n = sc.nextInt();
        m = sc.nextInt();
        sc.nextLine();
        arr = new char[n][m];
        isVsted = new boolean[n][m];
        int x = 0, y = 0;//起始点坐标
        for (int i = 0; i < n; i++) {
            String str = sc.nextLine();
            arr[i] = str.toCharArray();
            if (str.contains("S")) {
                x = i;
                y = str.indexOf("S");
            }
        }
        dexll d = BFS(x, y, 'T');//寻找三夫人所在的位置
        //找完之后初始化为都未走过
        for (int i = 0; i < isVsted.length; i++) {
            Arrays.fill(isVsted[i], false);
        }
        //起点到三夫人的步数+三夫人位置到终点的步数
        System.out.println(d.step + BFS(d.x, d.y, 'E').step);
    }
    /**
     * 
     * @param x 起点横坐标
     * @param y 起点纵坐标
     * @param T 终点位置
     * @return
     */
    public static dexll BFS(int x, int y, char T) {
        //模拟上下左右
        int[] dx = { 1, 0, -1, 0 };//横坐标
        int[] dy = { 0, 1, 0, -1 };//纵坐标
        LinkedList<dexll> list = new LinkedList<dexll>();
        isVsted[x][y] = true;//标记为已走过
        list.add(new dexll(x, y, 0));
        //当队列不为空的时候
        while (!list.isEmpty()) {
            dexll d = list.removeFirst();
            //找到终点
            if (arr[d.x][d.y] == T) {
                return d;
            }
            for (int i = 0; i < 4; i++) {
                int x1 = d.x + dx[i];
                int y1 = d.y + dy[i];
                //x1 >= 0 && y1 >= 0 && x1 < n && y1 < m 地图边界判断
                //!isVsted[x1][y1]该点没有走过
                //arr[x1][y1] != '1'该点不是障碍物
                if (x1 >= 0 && y1 >= 0 && x1 < n && y1 < m && !isVsted[x1][y1]
                        && arr[x1][y1] != '1') {
                    isVsted[x1][y1] = true;//标记为走过
                    list.add(new dexll(x1, y1, d.step + 1));
                }
            }
        }
        return null;
    }
}
class dexll {
    public int x;//横坐标
    public int y;//纵坐标
    public int step;//当前步数
    public dexll(int x, int y, int step) {
        this.x = x;
        this.y = y;
        this.step = step;
    }
}
```

## 迷宫

> 题目描述


下图给出了一个迷宫的平面图，其中标记为 1 的为障碍，标记为 0 的为可 以通行的地方。
010000
000100
001001
110000

迷宫的入口为左上角，出口为右下角，在迷宫中，只能从一个位置走到这 个它的上、下、左、右四个方向之一。

对于上面的迷宫，从入口开始，可以按DRRURRDDDR 的顺序通过迷宫， 一共 10 步。其中 D、U、L、R 分别表示向下、向上、向左、向右走。

对于下面这个更复杂的迷宫（30 行 50 列），请找出一种通过迷宫的方式， 其使用的步数最少，在步数最少的前提下，请找出字典序最小的一个作为答案。 请注意在字典序中D<L<R<U。（如果你把以下文字复制到文本文件中，请务 必检查复制的内容是否与文档中的一致。在试题目录下有一个文件 maze.txt， 内容与下面的文本相同）

01010101001011001001010110010110100100001000101010
00001000100000101010010000100000001001100110100101
01111011010010001000001101001011100011000000010000
01000000001010100011010000101000001010101011001011
00011111000000101000010010100010100000101100000000
11001000110101000010101100011010011010101011110111
00011011010101001001001010000001000101001110000000
10100000101000100110101010111110011000010000111010
00111000001010100001100010000001000101001100001001
11000110100001110010001001010101010101010001101000
00010000100100000101001010101110100010101010000101
11100100101001001000010000010101010100100100010100
00000010000000101011001111010001100000101010100011
10101010011100001000011000010110011110110100001000
10101010100001101010100101000010100000111011101001
10000000101100010000101100101101001011100000000100
10101001000000010100100001000100000100011110101001
00101001010101101001010100011010101101110000110101
11001010000100001100000010100101000001000111000010
00001000110000110101101000000100101001001000011101
10100101000101000000001110110010110101101010100001
00101000010000110101010000100010001001000100010101
10100001000110010001000010101001010101011111010010
00000100101000000110010100101001000001000000000010
11010000001001110111001001000011101001011011101000
00000110100010001000100000001000011101000000110011
10101000101000100010001111100010101001010000001000
10000010100101001010110000000100101010001011101000
00111100001000010000000110111000000001000000001011
10000001100111010111010001000110111010101101111000

> 答案提交


这是一道结果填空的题，你只需要算出结果后提交即可。本题的结果为一 个字符串，包含四种字母 D、U、L、R，在提交答案时只填写这个字符串，填 写多余的内容将无法得分

```java
import java.awt.Point;
import java.io.BufferedReader;
import java.io.FileReader;
import java.io.IOException;
import java.io.Reader;
import java.util.LinkedList;
import java.util.Queue;

public class 迷宫 {
	static char[] dir = {'D', 'L', 'R', 'U'};
	static int[][] next = {{1, 0}, {0, -1}, {0, 1}, {-1, 0}};
	static int[][] isvisted = new int[30][50];
	
	public static void main(String[] args) throws IOException {
		char[][] mig = new char[30][];
		Reader fr = new FileReader("C:\\WorkDir\\eclipse\\LanQiao\\src\\Y2019\\maze.txt");
		String str = null;
		int index = 0;
		try (BufferedReader br = new BufferedReader(fr)) {
			while ((str = br.readLine()) != null) {
				mig[index++] = str.toCharArray();
			}
		}
		bfs(mig);
	}
	
	public static void bfs(char[][] mig) {
		
		Queue<Point> queue = new LinkedList<>();
		queue.add(new Point(0, 0));
		String[][] str = new String[30][50];
		for (int i = 0; i < 30; i++) {
			for (int j = 0; j < 50; j++) {
				str[i][j] = "";
			}
		}
		
		while(!queue.isEmpty()) {
			Point cur = queue.poll();
			for (int i = 0; i < 4; i++) {
				int dx = cur.x + next[i][0];
				int dy = cur.y + next[i][1];
				// 不越界，没访问过，能走
				if (dx >= 0 && dx < 30 && dy >= 0 && dy < 50 && mig[dx][dy] == '0' && isvisted[dx][dy] == 0) {
					isvisted[dx][dy] = 1;
					queue.add(new Point(dx, dy));
					str[dx][dy] = str[cur.x][cur.y] + dir[i];
				}
			}
		}
		System.out.println(str[29][49]);
		
	}

}
```
