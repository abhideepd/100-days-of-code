# 100 Days Of Code - Log

### Day 0: April 1, 2024

**Today's Progress**: Committed on twitter, about starting this challenge. Forgot to use the hashtag 'learninpublic', then got to know, edit is a premium feature :-( 

**Thoughts:** Well, this will help me stay accountable. I had done this challenge previously. Although, its was a bit tough, still managed to stay alive until the 35th day, then had to end it due to a health sanfu.

**Link to work:** [My Twitter Post]([http://www.example.com](https://twitter.com/BHIDEEP/status/1774741988182532345))

### Day 1: April 2, 2024

**Today's Progress**: Successfully submitted the java solution for the question in cses problem set for trees, Distance Queries [link](https://cses.fi/problemset/task/1135)

**Thoughts:** Well, its a pretty basic tree question, where nodes of the tree are given, and a list of queries are given, from a to b. So, the most basic bruteforce way of approaching the problem is, to find the lca, and add the distance from lca to a and lca to b. In order to optimize this solution, we use the binary jumping technique. However, the challenging part arrived, when, all the test cases were passing except one of them. This puzzled me a lot, reverifying, that I have applied the technique and its covering all the edge cases. Then, while being in the rabbit hole, I found out, about the euler tour technique. However, before using this technique, to optimize my solution, I decided, to give it some more trial, as the time was just some milli seconds appart. While disecting the binary jumping solution, I did everything I could, like use arrays, reduce variables all that I can, still, no good. Then I suddenly, got access to a memory, I had read somewhere, that, in most cases, recursive approach takes more time than iterative approach, because, the heap needs to be cleared. That's when I thought, why not do the dfs, iteratively! rather than using the heap. So at first, I used the collections library for Stack, which didn't work. Then I created a new data structure, using array as the datastructure for my custom stack interface, I came close, still, not 100% Accepted. So I for some reason, assumed, that array is a memory block, and in order to assign it, there might be some time utilized to find that block. So, why not replace array with Linked List, that way, this time might be saved! And voila! It worked! I checked it multiple times, and its passing all the test cases! Although, I am not completely sure, what's the reason for the recursive solution and the Array or collections datastructure not working....

**Link to work:** Well, I haven't uploaded the code yet, however, pasting it below
<details>
  <summary>Accepted Solution</summary>
  
  ```java
  
import java.io.*;
import java.util.ArrayList;

public class DistanceQueriesCSES {
    static int LVL_LIMIT=20;
    static int parentMatrix[][];
    static int depth[];
    static ArrayList<Integer> tree[];
    static PrintWriter pw=new PrintWriter(System.out);
    public static void main(String[] args)throws IOException {
        FastReader x=new FastReader();
        int n=x.nextInt();
        int q=x.nextInt();
        parentMatrix=new int[n+1][LVL_LIMIT];
        tree=new ArrayList[n+1];
        tree[1]=new ArrayList<>();
        depth=new int[n+1];
        for(int i=0;i<n-1;i++){
            int a=x.nextInt();
            int b=x.nextInt();
            if(tree[a]==null){
                tree[a]= new ArrayList<>();
            }
            if(tree[b]==null){
                tree[b]= new ArrayList<>();
            }
            tree[a].add(b);
            tree[b].add(a);
        }
        buildTree();
        StringBuilder output=new StringBuilder();
        for(int i=0;i<q;i++){
            int a=x.nextInt();
            int b=x.nextInt();
            output.append(depth[a]+depth[b]-2*depth[getLCA(a,b)]);
            output.append("\n");
        }
        pw.println(output);
        pw.close();
    }
    static void dfsIterative(int node, int parent){
        StackLinkedList q=new StackLinkedList();
        q.push(node);
        q.push(parent);
        while(!q.isEmpty()){
            parent=(int)q.pop();
            node=(int)q.pop();
            parentMatrix[node][0]=parent;
            depth[node]+=depth[parent]+1;
            for(int child:tree[node]){
                if(child!=parent){
                    q.push(child);
                    q.push(node);
                }
            }
        }
    }
    static void binaryLifting(){
        for(int level=1;level<LVL_LIMIT;level++){
            for(int i=1;i<parentMatrix.length;i++){
                if(parentMatrix[i][level-1]==-1)return;
                parentMatrix[i][level]=parentMatrix[parentMatrix[i][level-1]][level-1];
            }
        }
    }
    static void buildTree(){
        dfsIterative(1, 0);
        binaryLifting();
    }
    static int jump(int a, int height, int matrix[][]){
        int level=0;
        while(height!=0){
            if((height&1)==1){
                a=matrix[a][level];
            }
            ++level;
            height=height>>1;
        }
        return a;
    }
    static int getLCA(int a, int b){
        if(depth[a]>depth[b]){
            int temp=a;
            a=b;
            b=temp;
        }
        b=jump(b, (depth[b]-depth[a]), parentMatrix);
        if(a==b){
            return a;
        }
        for(int level=LVL_LIMIT-1;level>=0;level--){
            int aa=parentMatrix[a][level], bb=parentMatrix[b][level];
            if(aa!=bb){
                a=aa;
                b=bb;
            }
        }
        return parentMatrix[a][0];
    }
// Simple I/O library, for speed. Easily available online
    static class FastReader {
        final private int BUFFER_SIZE = 1 << 16;
        private DataInputStream din;
        private byte[] buffer;
        private int bufferPointer, bytesRead;

        public FastReader() {
            din = new DataInputStream(System.in);
            buffer = new byte[BUFFER_SIZE];
            bufferPointer = bytesRead = 0;
        }

        public FastReader(String file_name) throws IOException {
            din = new DataInputStream(new FileInputStream(file_name));
            buffer = new byte[BUFFER_SIZE];
            bufferPointer = bytesRead = 0;
        }

        public String nextLine() {
            try{
                byte[] buf = new byte[10000000]; // line length
                int cnt = 0, c;
                while ((c = read()) != -1) {
                    if (c == '\n')
                        break;
                    buf[cnt++] = (byte) c;
                }
                return new String(buf, 0, cnt);
            }catch (Exception e){
                System.out.println(e.getMessage());
                return null;
            }
        }

        public int nextInt()  {
            int ret = 0;
            try {
                byte c = read();
                while (c <= ' ')
                    c = read();
                boolean neg = (c == '-');
                if (neg) c = read();
                do{
                    ret = ret * 10 + c - '0';
                }  while ((c = read()) >= '0' && c <= '9');

                if (neg) return -ret;
                return ret;
            } catch (Exception e) {
                System.out.println(e.getMessage());
                return -1;
            }
        }

        public long nextLong()   {

            try {
                long ret = 0;
                byte c = read();
                while (c <= ' ') c = read();
                boolean neg = (c == '-');
                if (neg)
                    c = read();
                do {
                    ret = ret * 10 + c - '0';
                }
                while ((c = read()) >= '0' && c <= '9');
                if (neg)
                    return -ret;
                return ret;
            } catch (Exception e) {
                System.out.println(e.getMessage());
                return -1;
            }
        }

        public double nextDouble()  {

            try {
                double ret = 0, div = 1;
                byte c = read();
                while (c <= ' ')
                    c = read();
                boolean neg = (c == '-');
                if (neg) c = read();

                do {
                    ret = ret * 10 + c - '0';
                }
                while ((c = read()) >= '0' && c <= '9');
                if (c == '.') {
                    while ((c = read()) >= '0' && c <= '9') {
                        ret += (c - '0') / (div *= 10);
                    }
                }

                if (neg) return -ret;
                return ret;
            } catch (Exception e) {
                System.out.println(e.getMessage());
                return -1;
            }
        }

        private void fillBuffer() throws IOException{
            bytesRead = din.read(buffer, bufferPointer = 0, BUFFER_SIZE);
            if (bytesRead == -1)
                buffer[0] = -1;
        }

        private byte read() throws IOException  {
            try{
                if (bufferPointer == bytesRead)
                    fillBuffer();
                return buffer[bufferPointer++];
            }catch(Exception e){
                System.out.println(e.getMessage());
                return -1;
            }
        }

        public void close() throws IOException {
            if (din == null)
                return;
            din.close();
        }
    }
    static class StackLinkedList {
        private class Node {
            int data;
            Node link;
        }
        Node top;
        StackLinkedList()
        {
            this.top = null;
        }
        // Using this function we will be pushing elements into the stack
        public void push(int x)
        {
            Node temp = new Node();
            temp.data = x;
            temp.link = top;
            top = temp;
        }
        // Using this function we will be checking whether the stack is empty or not
        public boolean isEmpty()
        {
            return top == null;
        }
        // using this function we will return the top element of the stack
        public int peek()
        {
            return top.data;
        }
        // Using this function we will pop the top element of the stack
        public void popp()
        {
            if (top == null) {
                System.out.print("\nStack Underflow");
                return;
            }
            top = (top).link;
        }
        public int pop(){
            int val=this.peek();
            this.popp();
            return val;
        }
    }
}
```
</details>
<details>
  <summary>Some other experiments, helping me to arrive to the solution</summary>
  
  ```java
  
import java.io.*;
import java.util.ArrayList;
import java.util.Arrays;
import java.util.Stack;
import java.io.DataInputStream;
import java.io.FileInputStream;
import java.io.IOException;
import java.util.Arrays;
import java.util.Random;

public class DistanceQueries {
    public static void main(String[] args)throws IOException {
        new Solution1();
    }

    static class Solution1{
        static int LVL_LIMIT=20;
        static PrintWriter pw=new PrintWriter(System.out);
        Solution1() throws IOException{
            String s="binaryLifting\\src\\testFile.txt";
            FastReader x=new FastReader();
//        x=new FastReader(s);
            int n=x.nextInt();
            int q=x.nextInt();
            int matrixTree[][]=new int[n+1][LVL_LIMIT];
            ArrayList<Integer> arr[]=new ArrayList[n+1];
            arr[1]=new ArrayList<>();
            int depth[]=new int[n+1];
            for(int i=0;i<n-1;i++){
                int a=x.nextInt();
                int b=x.nextInt();
                if(arr[a]==null){
                    arr[a]= new ArrayList<>();
                }
                if(arr[b]==null){
                    arr[b]= new ArrayList<>();
                }
                arr[a].add(b);
                arr[b].add(a);
            }
            buildTree(matrixTree, depth, arr);
//            for(int i[]:matrixTree){
//                for(int j:i){
//                    System.out.print(j+" ");
//                }
//                System.out.println();
//            }
            StringBuilder output=new StringBuilder();
            for(int i=0;i<q;i++){
                int a=x.nextInt();
                int b=x.nextInt();
                output.append(depth[a]+depth[b]-2*depth[getLCA(a,b,depth,matrixTree)]);
                output.append("\n");
//                pw.println(depth[a]+depth[b]-2*depth[getLCA(a,b,depth,matrixTree)]);
            }
            pw.println(output);
            pw.close();
        }
        static void buildTree(int[][] parentMatrix, int[] depth, ArrayList<Integer>[] hm){
            class method{
                void dfsIterative(int node, int parent){
//                    Stack q=new Stack();
//                    StackArray q=new StackArray();
                    StackLinkedList q=new StackLinkedList();
                    q.push(node);
                    q.push(parent);
                    while(!q.isEmpty()){
                        parent=(int)q.pop();
                        node=(int)q.pop();
                        parentMatrix[node][0]=parent;
                        depth[node]+=depth[parent]+1;
                        for(int child:hm[node]){
                            if(child!=parent){
                                q.push(child);
                                q.push(node);
                            }
                        }
                    }
                }
                void dfs(int node, int parent){
                    parentMatrix[node][0]=parent;
                    depth[node]+=depth[parent]+1;
                    if(parent!=-1){
                    }
                    for(int child:hm[node]){
                        if(child!=parent){
                            dfs(child, node);
                        }
                    }
                }
                void binaryLifting(){
                    for(int level=1;level<LVL_LIMIT;level++){
                        for(int i=1;i<parentMatrix.length;i++){
                            if(parentMatrix[i][level-1]==-1)return;
                            parentMatrix[i][level]=parentMatrix[parentMatrix[i][level-1]][level-1];
                        }
                    }
                }
            }
//            new method().dfs(1, 0);
            new method().dfsIterative(1, 0);
            new method().binaryLifting();
        }
        static int getLCA(int a, int b, int depth[], int matrix[][]){
            class abc{
                int jump(int a, int height, int matrix[][]){
                    int level=0;
                    while(height!=0){
                        if((height&1)==1){
                            a=matrix[a][level];
                        }
                        ++level;
                        height=height>>1;
                    }
                    return a;
                }
            }
            if(depth[a]>depth[b]){
                int temp=a;
                a=b;
                b=temp;
            }
            b=new abc().jump(b, (depth[b]-depth[a]), matrix);
            if(a==b){
                return a;
            }
            for(int level=LVL_LIMIT-1;level>=0;level--){
                int aa=matrix[a][level], bb=matrix[b][level];
                if(aa!=bb){
                    a=aa;
                    b=bb;
                }
            }
            return matrix[a][0];
        }
        static class FastReader {
            final private int BUFFER_SIZE = 1 << 16;
            private DataInputStream din;
            private byte[] buffer;
            private int bufferPointer, bytesRead;

            public FastReader() {
                din = new DataInputStream(System.in);
                buffer = new byte[BUFFER_SIZE];
                bufferPointer = bytesRead = 0;
            }

            public FastReader(String file_name) throws IOException {
                din = new DataInputStream(new FileInputStream(file_name));
                buffer = new byte[BUFFER_SIZE];
                bufferPointer = bytesRead = 0;
            }

            public String nextLine() {
                try{
                    byte[] buf = new byte[10000000]; // line length
                    int cnt = 0, c;
                    while ((c = read()) != -1) {
                        if (c == '\n')
                            break;
                        buf[cnt++] = (byte) c;
                    }
                    return new String(buf, 0, cnt);
                }catch (Exception e){
                    System.out.println(e.getMessage());
                    return null;
                }
            }

            public int nextInt()  {
                int ret = 0;
                try {
                    byte c = read();
                    while (c <= ' ')
                        c = read();
                    boolean neg = (c == '-');
                    if (neg) c = read();
                    do{
                        ret = ret * 10 + c - '0';
                    }  while ((c = read()) >= '0' && c <= '9');

                    if (neg) return -ret;
                    return ret;
                } catch (Exception e) {
                    System.out.println(e.getMessage());
                    return -1;
                }
            }

            public long nextLong()   {

                try {
                    long ret = 0;
                    byte c = read();
                    while (c <= ' ') c = read();
                    boolean neg = (c == '-');
                    if (neg)
                        c = read();
                    do {
                        ret = ret * 10 + c - '0';
                    }
                    while ((c = read()) >= '0' && c <= '9');
                    if (neg)
                        return -ret;
                    return ret;
                } catch (Exception e) {
                    System.out.println(e.getMessage());
                    return -1;
                }
            }

            public double nextDouble()  {

                try {
                    double ret = 0, div = 1;
                    byte c = read();
                    while (c <= ' ')
                        c = read();
                    boolean neg = (c == '-');
                    if (neg) c = read();

                    do {
                        ret = ret * 10 + c - '0';
                    }
                    while ((c = read()) >= '0' && c <= '9');
                    if (c == '.') {
                        while ((c = read()) >= '0' && c <= '9') {
                            ret += (c - '0') / (div *= 10);
                        }
                    }

                    if (neg) return -ret;
                    return ret;
                } catch (Exception e) {
                    System.out.println(e.getMessage());
                    return -1;
                }
            }

            private void fillBuffer() throws IOException{
                bytesRead = din.read(buffer, bufferPointer = 0, BUFFER_SIZE);
                if (bytesRead == -1)
                    buffer[0] = -1;
            }

            private byte read() throws IOException  {
                try{
                    if (bufferPointer == bytesRead)
                        fillBuffer();
                    return buffer[bufferPointer++];
                }catch(Exception e){
                    System.out.println(e.getMessage());
                    return -1;
                }
            }

            public void close() throws IOException {
                if (din == null)
                    return;
                din.close();
            }
        }
    }
    static class Solution2 {

        static int[][] up = new int[200005][20];
        static int[] level;

        public static void binary_lifting(int src, int par, ArrayList<ArrayList<Integer>> al, int l) {
            up[src][0] = par;
            level[src] = l;
            for(int i = 1; i < 20; i++) {
                if(up[src][i-1] != -1) {
                    up[src][i] = up[up[src][i-1]][i-1];
                } else {
                    up[src][i] = -1;
                }
            }

            for(int j: al.get(src)) {
                if(j != par)
                    binary_lifting(j, src, al, l+1);
            }
        }

        public static int find_ancestor(int src, int k) {
            if(src == -1 || k == 0) {
                return src;
            }
            for(int i = 19; i >= 0; i--) {
                if(k >= (1 << i)) {
                    return find_ancestor(up[src][i], k - (1 << i));
                }
            }
            return -1;
        }

        public static int lca(int u, int v) {
            if(u == v) return u;

            if(level[u] > level[v]) {
                int temp = v;
                v = u;
                u = temp;
            }

            int x = level[v] - level[u];
            v = find_ancestor(v, x);

            if(u==v) return u;

            for(int i = 19; i >= 0; i--) {
                if(up[u][i] != up[v][i]) {
                    u = up[u][i];
                    v = up[v][i];
                }
            }

            return find_ancestor(u, 1);
        }

        public static int find_distance(int u, int v) {
            int lca = lca(u, v);
            return level[u] + level[v] - 2*level[lca];
        }

        Solution2() throws IOException {
            BufferedReader br = new BufferedReader(new InputStreamReader(System.in));
            String[] s = br.readLine().split(" ");
            int n = Integer.parseInt(s[0]);
            int q = Integer.parseInt(s[1]);
            ArrayList<ArrayList<Integer>> al = new ArrayList<>();
            for(int i = 0; i < n; i++) al.add(new ArrayList<>());
            for(int i = 0; i < n-1; i++) {
                String[] str = br.readLine().split(" ");
                int u = Integer.parseInt(str[0]) - 1;
                int v = Integer.parseInt(str[1]) - 1;
                al.get(u).add(v);
                al.get(v).add(u);
            }

            level = new int[n];
            level[0] = 0;

            for(int[] a: up) Arrays.fill(a, -1);

            binary_lifting(0, -1, al, 0);

            for(int i = 0; i < q; i++) {
                String[] str = br.readLine().split(" ");
                int u = Integer.parseInt(str[0]) - 1;
                int v = Integer.parseInt(str[1]) - 1;
                System.out.println(find_distance(u, v));
            }
        }
    }
    static class StackLinkedList {
        private class Node {
            int data;
            Node link;
        }
        Node top;
        StackLinkedList()
        {
            this.top = null;
        }
        // Using this function we will be pushing elements into the stack
        public void push(int x)
        {
            Node temp = new Node();
            temp.data = x;
            temp.link = top;
            top = temp;
        }
        // Using this function we will be checking whether the stack is empty or not
        public boolean isEmpty()
        {
            return top == null;
        }
        // using this function we will return the top element of the stack
        public int peek()
        {
            return top.data;
        }
        // Using this function we will pop the top element of the stack
        public void popp()
        {
            if (top == null) {
                System.out.print("\nStack Underflow");
                return;
            }
            top = (top).link;
        }
        public int pop(){
            int val=this.peek();
            this.popp();
            return val;
        }
    }
    static class StackArray {
        int top;
        int maxsize = 2*100000+10;
        int[] arr = new int[maxsize];


        boolean isEmpty()
        {
            return (top < 0);
        }
        StackArray()
        {
            top = -1;
        }
        boolean push (int val)
        {
            arr[++top]=val;
            return true;
        }
        int pop ()
        {
            return arr[top--];
        }
    }

    static class Solution3 {

        static int n;
        static int q;
        static int h;
        static int time;
        static int[][] parents;
        static int[][] edges;
        static int[][] g;
        static int[] parent;
        static int[] depth;
        static int[] in;
        static int[] out;

        Solution3() throws IOException {
            final FastReader fs = new FastReader();
            n = fs.nextInt();
            q = fs.nextInt();
            h = 18;
            edges = new int[n - 1][2];
            for (int i = 0; i < (n - 1); i++) {
                edges[i] = new int[] { fs.nextInt() - 1, fs.nextInt() - 1 };
            }
            g = packG();
            parent = new int[n];
            depth = new int[n];
            in = new int[n];
            out = new int[n];
            dfs1();
            dfs2();
            initParents();
            final StringBuilder sb = new StringBuilder();
            for (int i = 0; i < q; i++) {
                final int u = fs.nextInt() - 1;
                final int v = fs.nextInt() - 1;
                final int lca = getLca(u, v);
                final int res = depth[u] + depth[v] - 2 * depth[lca];
                sb.append(res);
                sb.append('\n');
            }
            System.out.println(sb);
        }

        private static void dfs1() {
            final int[] stack = new int[n];
            int stackIdx = 0;
            stack[stackIdx++] = 0;
            parent[0] = 0;
            depth[0] = 0;
            while (stackIdx > 0) {
                final int u = stack[--stackIdx];
                depth[u] = depth[parent[u]] + 1;
                for (int v : g[u]) {
                    if (v != parent[u]) {
                        parent[v] = u;
                        stack[stackIdx++] = v;
                    }
                }
            }
        }

        private static void dfs2() {
            final int[] stack = new int[n];
            final int[] idx = new int[n];
            int stackIdx = 0;
            stack[stackIdx++] = 0;
            while (stackIdx > 0) {
                final int u = stack[stackIdx - 1];
                if (idx[u] == 0) {
                    in[u] = time++;
                }
                if (idx[u] < g[u].length) {
                    final int v = g[u][idx[u]++];
                    if (parent[u] != v) {
                        stack[stackIdx++] = v;
                    }
                } else {
                    out[u] = time;
                    stackIdx--;
                }
            }
        }

        private static int[][] packG() {
            final int[][] g = new int[n][];
            final int[] size = new int[n];
            for (int[] edge : edges) {
                ++size[edge[0]];
                ++size[edge[1]];
            }
            for (int i = 0; i < n; i++) {
                g[i] = new int[size[i]];
            }
            for (int[] edge : edges) {
                g[edge[0]][--size[edge[0]]] = edge[1];
                g[edge[1]][--size[edge[1]]] = edge[0];
            }
            return g;
        }

        private static void initParents() {
            parents = new int[h + 1][n];
            parents[0] = parent;
            for (int i = 1; i <= h; i++) {
                for (int u = 0; u < n; u++) {
                    final int nodeParent = parents[i - 1][u];
                    parents[i][u] = parents[i - 1][nodeParent];
                }
            }
        }

        private static boolean isAncestor(int u, int v) {
            return in[u] <= in[v] && out[v] <= out[u];
        }

        private static int getLca(int u, int v) {
            if (isAncestor(u, v)) { return u; }
            if (isAncestor(v, u)) { return v; }
            for (int i = h; i >= 0; i--) {
                if (!isAncestor(parents[i][u], v)) {
                    u = parents[i][u];
                }
            }
            return parents[0][u];
        }

        static final class Utils {
            private static class Shuffler {
                private static void shuffle(int[] x) {
                    final Random r = new Random();

                    for (int i = 0; i <= x.length - 2; i++) {
                        final int j = i + r.nextInt(x.length - i);
                        swap(x, i, j);
                    }
                }

                private static void shuffle(long[] x) {
                    final Random r = new Random();

                    for (int i = 0; i <= x.length - 2; i++) {
                        final int j = i + r.nextInt(x.length - i);
                        swap(x, i, j);
                    }
                }

                private static void swap(int[] x, int i, int j) {
                    final int t = x[i];
                    x[i] = x[j];
                    x[j] = t;
                }

                private static void swap(long[] x, int i, int j) {
                    final long t = x[i];
                    x[i] = x[j];
                    x[j] = t;
                }
            }

            public static void shuffleSort(int[] arr) {
                Shuffler.shuffle(arr);
                Arrays.sort(arr);
            }

            public static void shuffleSort(long[] arr) {
                Shuffler.shuffle(arr);
                Arrays.sort(arr);
            }

            private static int[][] packG(int[][] edges, int n) {
                final int[][] g = new int[n][];
                final int[] size = new int[n];
                for (int[] edge : edges) {
                    ++size[edge[0]];
                }
                for (int i = 0; i < n; i++) {
                    g[i] = new int[size[i]];
                }
                for (int[] edge : edges) {
                    g[edge[0]][--size[edge[0]]] = edge[1];
                }
                return g;
            }

            private static int[][][] packGW(int[][] edges, int n) {
                final int[][][] g = new int[n][][];
                final int[] size = new int[n];
                for (int[] edge : edges) {
                    ++size[edge[0]];
                }
                for (int i = 0; i < n; i++) {
                    g[i] = new int[size[i]][2];
                }
                for (int[] edge : edges) {
                    g[edge[0]][--size[edge[0]]] = new int[] { edge[1], edge[2] };
                }
                return g;
            }

            private Utils() {}
        }

        static class FastReader {
            private static final int BUFFER_SIZE = 1 << 16;
            private final DataInputStream din;
            private final byte[] buffer;
            private int bufferPointer, bytesRead;

            FastReader() {
                din = new DataInputStream(System.in);
                buffer = new byte[BUFFER_SIZE];
                bufferPointer = bytesRead = 0;
            }

            FastReader(String file_name) throws IOException {
                din = new DataInputStream(new FileInputStream(file_name));
                buffer = new byte[BUFFER_SIZE];
                bufferPointer = bytesRead = 0;
            }

            public String readLine() throws IOException {
                final byte[] buf = new byte[1024]; // line length
                int cnt = 0, c;
                while ((c = read()) != -1) {
                    if (c == '\n') {
                        break;
                    }
                    buf[cnt++] = (byte) c;
                }
                return new String(buf, 0, cnt);
            }

            public int nextSign() throws IOException {
                byte c = read();
                while ('+' != c && '-' != c) {
                    c = read();
                }
                return '+' == c ? 0 : 1;
            }

            private static boolean isSpaceChar(int c) { return !(c >= 33 && c <= 126); }

            private int skip() throws IOException {
                int b;
                //noinspection StatementWithEmptyBody
                while ((b = read()) != -1 && isSpaceChar(b)) {}
                return b;
            }

            public char nc() throws IOException {
                return (char) skip();
            }

            public String next() throws IOException {
                int b = skip();
                final StringBuilder sb = new StringBuilder();
                while (!isSpaceChar(b)) { // when nextLine, (isSpaceChar(b) && b != ' ')
                    sb.appendCodePoint(b);
                    b = read();
                }
                return sb.toString();
            }

            public int nextInt() throws IOException {
                int ret = 0;
                byte c = read();
                while (c <= ' ') {
                    c = read();
                }
                final boolean neg = c == '-';
                if (neg) { c = read(); }
                do {
                    ret = ret * 10 + c - '0';
                } while ((c = read()) >= '0' && c <= '9');

                if (neg) { return -ret; }
                return ret;
            }

            public long nextLong() throws IOException {
                long ret = 0;
                byte c = read();
                while (c <= ' ') { c = read(); }
                final boolean neg = c == '-';
                if (neg) { c = read(); }
                do {
                    ret = ret * 10 + c - '0';
                }
                while ((c = read()) >= '0' && c <= '9');
                if (neg) { return -ret; }
                return ret;
            }

            public double nextDouble() throws IOException {
                double ret = 0, div = 1;
                byte c = read();
                while (c <= ' ') { c = read(); }
                final boolean neg = c == '-';
                if (neg) { c = read(); }

                do {
                    ret = ret * 10 + c - '0';
                }
                while ((c = read()) >= '0' && c <= '9');

                if (c == '.') {
                    while ((c = read()) >= '0' && c <= '9') {
                        ret += (c - '0') / (div *= 10);
                    }
                }

                if (neg) { return -ret; }
                return ret;
            }

            private void fillBuffer() throws IOException {
                bytesRead = din.read(buffer, bufferPointer = 0, BUFFER_SIZE);
                if (bytesRead == -1) { buffer[0] = -1; }
            }

            private byte read() throws IOException {
                if (bufferPointer == bytesRead) { fillBuffer(); }
                return buffer[bufferPointer++];
            }

            public void close() throws IOException {
                din.close();
            }
        }
    }
}
  ```
</details>
