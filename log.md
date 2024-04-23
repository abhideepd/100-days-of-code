# 100 Days Of Code - Log

### Day 0: April 1, 2024

**Today's Progress**: Committed on twitter, about starting this challenge. Forgot to use the hashtag 'learninpublic', then got to know, edit is a premium feature :-( 

**Thoughts:** Well, this will help me stay accountable. I had done this challenge previously. Although, its was a bit tough, still managed to stay alive until the 35th day, then had to end it due to a health sanfu.

**Link to work:** [My Twitter Post]([http://www.example.com](https://twitter.com/BHIDEEP/status/1774741988182532345))

### Day 1: April 2, 2024

**Today's Progress**: Successfully submitted the java solution for the question in cses problem set for trees, Distance Queries [link](https://cses.fi/problemset/task/1135)

**Thoughts:** Well, its a pretty basic tree question, where nodes of the tree are given, and a list of queries are given, from a to b, and we have to find the distance between them. So, the most basic bruteforce way of approaching the problem is, to find the lca, and add the distance from lca to a and lca to b. In order to optimize this solution, we use the binary jumping technique. However, the challenging part arrived, when, all the test cases were passing except one of them. This puzzled me a lot, reverifying, that I have applied the technique and its covering all the edge cases. Then, while being in the rabbit hole, I found out, about the euler tour technique. However, before using this technique, to optimize my solution, I decided, to give it some more trial, as the time was just some milli seconds appart. While disecting the binary jumping solution, I did everything I could, like use arrays, reduce variables all that I can, still, no good. Then I suddenly, got access to a memory, I had read somewhere, that, in most cases, recursive approach takes more time than iterative approach, because, the heap needs to be cleared. That's when I thought, why not do the dfs, iteratively! rather than using the heap. So at first, I used the collections library for Stack, which didn't work. Then I created a new data structure, using array as the datastructure for my custom stack interface, I came close, still, not 100% Accepted. So I for some reason, assumed, that array is a memory block, and in order to assign it, there might be some time utilized to find that block. So, why not replace array with Linked List, that way, this time might be saved! And voila! It worked! I checked it multiple times, and its passing all the test cases! Although, I am not completely sure, what's the reason for the recursive solution and the Array or collections datastructure not working....

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

### Day 2: April 3, 2024

**Today's Progress**: Successfully solved a long time upsolve backlog problem (Maximize value of Function in a ball passing game), [link]((https://leetcode.com/problems/maximize-value-of-function-in-a-ball-passing-game/))

**Thoughts:** Really enjoyed solving this problem, this problem led me to explore the concept of binary jumping. Understanding this problem is easy, however, the trick is in the implementation, where we have to optimize space as well as time. If you see the question, you can clearly, see the recursive pattern. I did too! and used the recursion. And as expected, started failing when the test cases were going to the higher limit. So, too the next step, memoised the recursive steps (dynamic programming). So, as expected, this went far, however started failing for higher limit test cases(higher than the previous), due to space. See, in the question, the value of k is 10^10, and arrays can't go with size that high. So, initially, I was surprised, because, I sincerely thought, this was a small problem, why is it failing, took multiple attempts to solve it, even tried iterative dp and tried to minimize the array space as much as possible, however, I wasn't able to get all the test cases accepted. So, I started researching the solutions, since editorial is not there. Then, I came across binary jumping. Once, I started taking this problem as a graph problem, lots of roadblocks automatically broke. First, I used floyd, circle finding algorithm (its not that complicated, infact highly intuitive. refer this), proceeded to find cycle of the input nodes, store them in hashset, then for each node, I calculated the solution and found the max. It passed nearly all the test, but not all! I explored, as to where I went wrong, I used this visualizer to analyse the input, and instantly got to know, where I went wrong, I assumed, that the whole graph, will be a single connected component, however, this input contained multiple connected components. So, now if I only use floyd's algorithm to solve, I would have to traverse and save each cycle, the cycle's sum and then proceed, which will make it complex and also, time complexity will be compromised (first, in order to search, if the nodes are in cycle or not, we will have to go through all the cycles, saved in hashset). Therefore, used the plain old, binary jumping, in its simplest form, and voila! it worked. If you are getting a bit confused reading this, I understand, I haven't explained in great detail and skipped the deeper parts, as its a rough high level overview. However, I have uploaded the three big approaches I was working on, which I feel, is better than the big ass explaination.

**Link to work:** Well, I haven't uploaded the code yet, however, pasting it below
<details>
  <summary>Simple Recursive Approach</summary>

  ```java

class Solution {
    public long getMaxFunctionValue(List<Integer> receiver, long k) {
        long ans=0;
        for(int i=0;i<receiver.size();i++){
            // System.out.println(i);
            ans=Math.max(ans, f(i, receiver, k));
        }
        // System.out.println("f(0): "+f(0,receiver,k));
        // System.out.println("f(1): "+f(1,receiver,k));
        // System.out.println("f(2): "+f(2,receiver,k));
        return ans;
    }
    long f(int n, List<Integer> receiver, long k){
        if(k==-1){
            return 0;
        }
        return n+f(receiver.get(n), receiver, k-1);
    }
}
  ```
</details>
<details>
  <summary>Recursive Memoisation Approach</summary>
  
  ```java

class Solution {
    public long getMaxFunctionValue(List<Integer> receiver, long k) {
        if(receiver.size()==1)return 0;
        return abc(receiver, k);
    }
    long dp[][];
    long abc(List<Integer> receiver, long k){
        long ans=0;
        dp=new long[receiver.size()][1000];
        for(int i=0;i<receiver.size();i++){
            ans=Math.max(ans, ff(i, receiver, k));
        }
        return ans;
    }
    long ff(int n, List<Integer> receiver, long k){
        if(k==-1){
            return 0;
        }
        if(dp[n][(int)k]!=0){
            return dp[n][(int)k];
        }
        dp[n][(int)k]= n+ff(receiver.get(n), receiver, k-1);
        return dp[n][(int)k];
    }
}
  ```
</details>
<details>
  <summary>Floyd's algorithm Approach</summary>

  ```java

class Solution {
    ArrayList<Integer> tree[];
    ArrayList<Integer> succ;
    int sumOfCycle, lengthOfCycle, cycleFirst;
    HashSet<Integer> cycleElems;
    int prefixSum[];
    public long getMaxFunctionValue(List<Integer> receiver, long k) {
        tree=new ArrayList[receiver.size()];
        cycleElems=new HashSet<Integer>();
        succ=new ArrayList<Integer>(receiver);
        for(int i=0;i<receiver.size();i++){
            tree[i]=new ArrayList<Integer>();
        }
        for(int i=0;i<receiver.size();i++){
            tree[i].add(receiver.get(i));
        }
        floyd(0);
        long max=0;
        for(int i=0;i<receiver.size();i++){
            long sum=cycleElems.contains(i)?solve2(i,k):solve11(i,k);
            // System.out.println(i+": "+sum);
            max=Math.max(sum,max);
        }
        return max;
    }
    void floyd(int x){
        int a = succ(x);
        int b = succ(succ(x));
        while (a != b) {
            a = succ(a);
            b = succ(succ(b));
        }
        a = x;
        while (a != b) {
            a = succ(a);
            b = succ(b);
        }
        int first = a;
        b = succ(a);
        int length = 1;
        int sum=first;
        cycleFirst=first;
        while (a != b) {
            cycleElems.add(b);
            sum+=b;
            b = succ(b);
            length++;
        }
        cycleElems.add(first);
        sumOfCycle=sum;
        lengthOfCycle=length;
    }
    long solve11(int index, long k){
        long sum=0; int ind=index;
        while(k>0){
            if(cycleElems.contains(index)){
                break;
            }
            --k;
            index=succ(index);
            sum+=index;
        }
        return sum+solve2(index,k)+ind-index;
    }
    // outside cycle
    long solve1(int index, long k){
        long sum=0;int ind=index;
        while(k>0){
            if(cycleElems.contains(index)){
                break;
            }
            --k;
            index=succ(index);
            sum+=index;
        }
        // System.out.println(sum+" -- "+k+" "+(k/lengthOfCycle));
        sum+=(sumOfCycle*(k/lengthOfCycle));
        // long temp=lengthOfCycle-(k%lengthOfCycle);
        long temp=(k%lengthOfCycle);
        // System.out.println(sum+" - "+temp+" "+k);
        index=cycleFirst;
        for(int i=0;i<temp;i++){
            sum+=index;
            index=succ(index);
        }
        // System.out.println("--> "+sum);
        return sum+ind;
    }
    // inside cycle
    long solve2(int index, long k){
        long sum=0;int ind=index;
        sum+=(sumOfCycle*(k/lengthOfCycle));
        long temp=(k%lengthOfCycle);
        index=succ(ind);
        for(int i=0;i<temp;i++){
            sum+=index;
            index=succ(index);
        }
        return sum+ind;
    }
    int succ(int x){
        return succ.get(x);
    }
}
  ```
</details>
<details>
  <summary>Binary Jumping Approach</summary>

  ```java

class Solution {
    public long getMaxFunctionValue(List<Integer> receiver, long k) {
        // return new Solution1().getMaxFunctionValue(receiver, k);
        return new Solution2().getMaxFunctionValue(receiver, k);
    }
}
class Solution1 {
    ArrayList<Integer> tree[];
    ArrayList<Integer> succ;
    int sumOfCycle, lengthOfCycle, cycleFirst;
    HashSet<Integer> cycleElems;
    public long getMaxFunctionValue(List<Integer> receiver, long k) {
        // tree=new ArrayList[receiver.size()];
        cycleElems=new HashSet<Integer>();
        succ=new ArrayList<Integer>(receiver);
        // for(int i=0;i<receiver.size();i++){
        //     tree[i]=new ArrayList<Integer>();
        // }
        // for(int i=0;i<receiver.size();i++){
        //     tree[i].add(receiver.get(i));
        // }
        floyd(0);
        long max=0;
        for(int i=0;i<receiver.size();i++){
            long sum=solve(i,k);
            System.out.println(i+": "+sum);
            max=Math.max(sum,max);
        }
        return max;
    }
    void floyd(int x){
        int a = succ(x);
        int b = succ(succ(x));
        while (a != b) {
            a = succ(a);
            b = succ(succ(b));
        }
        a = x;
        while (a != b) {
            a = succ(a);
            b = succ(b);
        }
        int first = a;
        b = succ(a);
        int length = 1;
        int sum=first;
        cycleFirst=first;
        while (a != b) {
            cycleElems.add(b);
            sum+=b;
            b = succ(b);
            length++;
        }
        cycleElems.add(first);
        sumOfCycle=sum;
        lengthOfCycle=length;
    }
    long solve(int i, long k){
        long sum=cycleElems.contains(i)?solveInsideCircle(i,k):solveOutsideCircle(i,k);
        return sum;
    }
    long solveOutsideCircle(int index, long k){
        long sum=0; int ind=index;
        while(k>0){
            if(cycleElems.contains(index)){
                break;
            }
            --k;
            index=succ(index);
            sum+=index;
        }
        return sum+solveInsideCircle(index,k)+ind-index;
    }
    long solveInsideCircle(int index, long k){
        long sum=0;int ind=index;
        sum+=(sumOfCycle*(k/lengthOfCycle));
        long temp=(k%lengthOfCycle);
        index=succ(ind);
        for(int i=0;i<temp;i++){
            sum+=index;
            index=succ(index);
        }
        return sum+ind;
    }
    int succ(int x){
        return succ.get(x);
    }
}
class Solution2{
    int binaryLifting[][];
    long sumLifting[][];
    int depth[];
    ArrayList<Integer> childOf;
    int n;
    int HEIGHT=35;
    public long getMaxFunctionValue(List<Integer> receiver, long k){
        n=receiver.size();
        childOf=new ArrayList<Integer>(receiver);
        binaryLifting=new int[n][HEIGHT];
        sumLifting=new long[n][HEIGHT];
        depth=new int[n];
        fillBinaryLifting();
        return solve(k);
    }
    long solve(long k){
        long max=0;
        for(int i=0;i<n;i++){
            long sum=getSum(i,k)+i;
            max=Math.max(max,sum);
        }
        return max;
    }
    long getSum(int i, long k){
        int index=i, level=0;
        long sum=0;
        while(k>0){
            if((k&1)==1){
                sum+=sumLifting[i][level];
                i=binaryLifting[i][level];
            }
            ++level;
            k=k>>1;
        }
        return sum;
    }
    void fillBinaryLifting(){
        HashSet<Integer> visited=new HashSet<Integer>();
        for(int i[]:binaryLifting)Arrays.fill(i,-1);
        for(int i=0;i<n;i++){
            if(!visited.contains(i)){
                dfs(i,childOf.get(i),visited);
            }
        }
        binaryLifting();
        // printBinaryLifting();
    }
    void printBinaryLifting(){
        int c=0;
        // for(int i[]:binaryLifting){
        for(long i[]:sumLifting){
            System.out.println();
            System.out.print(c+++": ");
            for(int j=0;j<5;j++){
                System.out.print(i[j]+" ");
            }
        }
    }
    void binaryLifting(){
        for(int level=1;level<HEIGHT;level++){
            for(int node=0;node<n;node++){
                if(binaryLifting[node][level-1]==-1)continue;
                binaryLifting[node][level]=binaryLifting[binaryLifting[node][level-1]][level-1];
                sumLifting[node][level]=sumLifting[node][level-1]+sumLifting[binaryLifting[node][level-1]][level-1];
            }
        }
    }
    void dfs(int currNode, int par, HashSet<Integer>  visited){
        visited.add(currNode);
        depth[currNode]=depth[par]+1;
        binaryLifting[currNode][0]=par;
        sumLifting[currNode][0]=par;
        if(childOf.get(currNode)!=par){
            dfs(childOf.get(currNode),currNode,visited);
        }
    }
}
  ```
</details>

### Day 3: April 4, 2024

**Today's Progress**: Solved this really challenging codeforces problem named Duff Army [link](https://codeforces.com/contest/588/problem/E)

**Thoughts**: Like the previous problems, listed above, this is also a variation of binary jumping problem. The problem was, to find the lowest 10 people, between a and b node. So implementing the binary jumping algorithm, by processing and storing the result, and then answering the query. It wasn't that tough. The challenging part for me was, to not use this algo & still pass 30 TCs

**Link(s) to work**: Below are the implementations I tried.

<details>
  <summary>Non binary jumping technique, passing 30 testcases</summary>
  
  ```java


import javax.xml.crypto.Data;
import java.io.*;
import java.util.*;
import java.net.HttpURLConnection;
import java.net.URL;

public class DuffArmy5 {
    static int MAX=(int)10e5+5;
    static int HEIGHT=(int)(Math.log(MAX)/Math.log(2))+1;
    static BufferedReader x;
    static int depth[]=new int[MAX];
    static int noOfPeople[];
    static DataStructure lowestTenPeople[];
    static int binaryLifting[][]=new int[MAX][HEIGHT];
    static int n;
    static ArrayList<Integer> tree[]=new ArrayList[MAX];
    static ArrayList<Integer> p[];
    static Tree T;
    static PrintWriter pw=new PrintWriter(System.out);
    public static void main(String[] args)throws IOException {
        URL url = new URL("http://example.com/api/endpoint");


        String file="D:\\usaco.guide\\binaryLifting\\testFile1.txt";
        x=new BufferedReader(new InputStreamReader(System.in));
//        x=new BufferedReader(new FileReader(file));
        StringTokenizer st=new StringTokenizer(x.readLine());
        n=Integer.parseInt(st.nextToken());
        int m=Integer.parseInt(st.nextToken());
        int q=Integer.parseInt(st.nextToken());
        p=new ArrayList[n+1];
        noOfPeople=new int[n+1];
        lowestTenPeople=new DataStructure[n+1];
        for(int i=0;i<MAX;i++){
            tree[i]=new ArrayList<>();
            if(i<= n){
                p[i]=new ArrayList<>();
                lowestTenPeople[i]=new DataStructure();
            }
        }
        for(int i=0;i<n-1;i++){
            st=new StringTokenizer(x.readLine());
            int u=Integer.parseInt(st.nextToken());
            int v=Integer.parseInt(st.nextToken());
            tree[u].add(v);
            tree[v].add(u);
        }
        st=new StringTokenizer(x.readLine());
        for(int i=1;i<=m;i++){
            int city=Integer.parseInt(st.nextToken());
            p[city].add(i);
            lowestTenPeople[city].add(i);
        }
        for(int i=1;i<=n;i++){
            lowestTenPeople[i].restructures();
            if(p[i].size()>10){
                ArrayList<Integer> temp=new ArrayList<>();
                for(int j=0;j<10;j++){
                    temp.add(p[i].get(j));
                }
                p[i]=new ArrayList<>(temp);
            }
        }
        T=new Tree();
//        T.printLowestTenPeople();
        for(int i=0;i<q;i++){
            st=new StringTokenizer(x.readLine());
            int v=Integer.parseInt(st.nextToken());
            int u=Integer.parseInt(st.nextToken());
            int a=Integer.parseInt(st.nextToken());
            getoutput1(v,u,a);
        }
        pw.close();
    }
    static void getoutput1(int v, int u, int a){
        int lca=T.LCA(v,u);
        int people1=noOfPeople[v]-noOfPeople[lca];
        int people2=noOfPeople[u]-noOfPeople[lca];
        int people=people1+people2+(p[lca].size());
        int k=Math.min(a,people);

        // get the just front child of lca, in both cases, remove values from a,b of noofpeople and thk
        // gets really complex, also, space takes up a lot.

        DataStructure temp1=new DataStructure();
        DataStructure temp2=new DataStructure();
        temp1.add(lowestTenPeople[lca]);
        System.out.println(temp1.value);
        temp1.remove(lowestTenPeople[v]);
//        System.out.println(temp1);
//        temp2.add(lowestTenPeople[u]);
//        temp2.remove(lowestTenPeople[lca]);
        temp1.remove(lowestTenPeople[u]);
//        ArrayList<Integer> temp=merge(temp1.value,temp2.value);
        ArrayList<Integer> tempp=merge(temp1.value,p[lca]);
        tempp=merge(tempp,p[lca]);
        k=Math.min(k,tempp.size());
        pw.print(k+" ");
        for(int i=0;i<k;i++){
            pw.print(tempp.get(i)+" ");
        }
        pw.println();
    }
    static ArrayList<Integer> merge(ArrayList<Integer> arr1, ArrayList<Integer> arr2) {
        int i = 0, j = 0, k = 0;
        ArrayList<Integer> temp=new ArrayList<>();
        int n1=arr1.size();
        int n2=arr2.size();
        while (i<n1 && j <n2 && temp.size()<=10)
        {
            if (arr1.get(i) < arr2.get(j))
                temp.add(arr1.get(i++));
            else
                temp.add(arr2.get(j++));
        }

        while (i < n1 && temp.size()<=10)
            temp.add(arr1.get(i++));

        while (j < n2 && temp.size()<=10)
            temp.add(arr2.get(j++));

        return temp;
    }
    static class Tree extends DuffArmy5 {
        Tree(){
            dfs(1,0);
        }
        static int LCA(int a, int b) {
            if(depth[a]>depth[b]){
                int temp=a;
                a=b;
                b=temp;
            }
            // depth of a is less than depth of b
            b=jump(b,depth[b]-depth[a]);
            if(a==b){
                return a;
            }
            for(int i=HEIGHT-1;i>=0;i--){
                if(binaryLifting[a][i]!=binaryLifting[b][i]){
                    a=binaryLifting[a][i];
                    b=binaryLifting[b][i];
                }
            }
            return binaryLifting[a][0];
        }
        static int jump(int a, int height){
            int level=0;
            while(height!=0){
                if((height&1)==1){
                    a=binaryLifting[a][level];
                }
                level+=1;
                height=height>>1;
            }
            return a;
        }
        static void printBinaryLifting(){
            for(int i=0;i<=n;i++){
                System.out.print(i+" : ");
                for(int j=0;j<HEIGHT;j++){
                    System.out.print(binaryLifting[i][j]+" ");
                }
                System.out.println();
            }
        }
        static void printLowestTenPeople(){
            for(DataStructure i:lowestTenPeople){
                System.out.println(i.value);
            }
        }
        static void binaryLifting(){
            for(int i=1;i<MAX;i++){
                for(int level=1;level<HEIGHT;level++){
                    binaryLifting[i][level]=binaryLifting[binaryLifting[i][level-1]][level-1];
                }
            }
        }
        static void dfs(int node, int par){
            binaryLifting[node][0]=par;
            depth[node]=depth[par]+1;
            noOfPeople[node]=noOfPeople[par]+(p[node].size());
            for(int level=1;level<HEIGHT;level++){
                binaryLifting[node][level]=binaryLifting[binaryLifting[node][level-1]][level-1];
            }
            for(int childNode:tree[node]){
                if(childNode==par)continue;
                dfs(childNode, node);
                lowestTenPeople[node].value=new ArrayList<>(merge(lowestTenPeople[node].value,lowestTenPeople[childNode].value));
            }
        }
    }
    static class DataStructure{
        ArrayList<Integer> value;
        DataStructure(){
            value=new ArrayList<>();
        }
        void add(int a){
            value.add(a);
        }
        void add(List<Integer> a){
            value.addAll(a);
        }
        void add(DataStructure d){
            value.addAll(d.value);
        }
        void restructure(){
            Collections.sort(value);
            if(value.size()<=10)return;
            ArrayList<Integer> temp=new ArrayList<>(value);
            value=new ArrayList<>();
            for(int i=0;i<10;i++){
                value.add(temp.get(i));
            }
        }
        void restructures(){
            if(value.size()<=10)return;
            ArrayList<Integer> temp=new ArrayList<>(value);
            value=new ArrayList<>();
            for(int i=0;i<10;i++){
                value.add(temp.get(i));
            }
        }
        void remove(DataStructure remove){
            ArrayList<Integer> parent=new ArrayList<>(value);
            ArrayList<Integer> removeThese=new ArrayList<>(remove.value);
            value=new ArrayList<>();
            int rem=0, par=0;
            while(par<parent.size()&&rem<removeThese.size()){
                while(rem<removeThese.size()&&par<parent.size()&&parent.get(par)==removeThese.get(rem)){
                    ++par;
                    ++rem;
                    if(value.size()>10)break;
                }
                while(rem<removeThese.size()&&par<parent.size()&&parent.get(par)<removeThese.get(rem)){
                    value.add(parent.get(par));
                    ++par;
                    if(value.size()>10)break;
                }
                while(rem<removeThese.size()&&par<parent.size()&&removeThese.get(rem)<parent.get(par)){
                    ++rem;
                    if(value.size()>10)break;
                }
                if(value.size()>10)break;
            }
            while(par<parent.size()&&value.size()<=10){
                value.add(parent.get(par++));
            }
        }
    }
}


  ```

</details>
<details>
  <summary>Accepted Solution</summary>
  
  ```java
  
import java.io.BufferedReader;
import java.io.IOException;
import java.io.InputStreamReader;
import java.io.PrintWriter;
import java.util.ArrayList;
import java.util.Collections;
import java.util.StringTokenizer;

public class DuffInTheArmyAccepted {
    static int n;
    static ArrayList<Integer> tree[];
    static ArrayList<Integer> peopleAt[];
    static ArrayList<Integer> peopleBinaryLifting[][];
    static Tree t;
    public static void main(String[] args)throws IOException {
        BufferedReader x=new BufferedReader(new InputStreamReader(System.in));
        PrintWriter pw=new PrintWriter(System.out);
        StringTokenizer st=new StringTokenizer(x.readLine());
        n=Integer.parseInt(st.nextToken());
        peopleBinaryLifting=new ArrayList[n+1][20];
        int m=Integer.parseInt(st.nextToken());
        int q=Integer.parseInt(st.nextToken());
        tree=new ArrayList[n+1];
        peopleAt=new ArrayList[n+1];
        for(int city=0;city<=n;city++){
            tree[city]=new ArrayList<>();
            peopleAt[city]=new ArrayList<>();
        }
        for(int i=0;i<n-1;i++){
            st=new StringTokenizer(x.readLine());
            int a=Integer.parseInt(st.nextToken());
            int b=Integer.parseInt(st.nextToken());
            tree[a].add(b);
            tree[b].add(a);
        }
        st=new StringTokenizer(x.readLine());
        for(int i=1;i<=m;i++){
            int city=Integer.parseInt(st.nextToken());
            peopleAt[city].add(i);
        }
        for(int city=1;city<=n;city++){
            ArrayList<Integer> arr=peopleAt[city];
            if(arr.size()<=10)continue;
            ArrayList<Integer> new_arr=new ArrayList<>();
            for(int i=0;i<10;i++){
                new_arr.add(arr.get(i));
            }
            peopleAt[city]=new ArrayList<>(new_arr);
        }
        t=new Tree();
//        t.printBinaryLiftingPeople();
        for(int i=0;i<q;i++){
            st=new StringTokenizer(x.readLine());
            int a=Integer.parseInt(st.nextToken());
            int b=Integer.parseInt(st.nextToken());
            int k=Integer.parseInt(st.nextToken());
            ArrayList<Integer> answer=solve(a,b);
            int kk=Math.min(k,answer.size());
            pw.print(kk+" ");
            for(int j=0;j<kk;j++){
                pw.print(answer.get(j)+" ");
            }
            pw.println();
        }
        pw.close();
    }
    static ArrayList<Integer> solve(int a, int b){
        int lca=t.LCA(a, b);
        ArrayList<Integer> people=new ArrayList<>();
        if(lca!=a)people.addAll(peopleAt[a]);
        if(lca!=b)people.addAll(peopleAt[b]);
        people.addAll(getPeople(a, t.depth[a]-t.depth[lca]-1));
        people.addAll(getPeople(b, t.depth[b]-t.depth[lca]-1));
        people.addAll(peopleAt[lca]);
//        System.out.println(a+","+b+": "+people);
        Collections.sort(people);
        return people;
    }
    static ArrayList<Integer> getPeople(int a, int height){
        ArrayList<Integer> list=new ArrayList<>();
        int level=0;
        while(height>0){
            if((height&1)==1){
                list.addAll(peopleBinaryLifting[a][level]);
                a=t.binaryLifting[a][level];
            }
            level+=1;
            height=height>>1;
        }
        return list;
    }
    static class Tree extends DuffInTheArmyAccepted {
        static int HEIGHT=20;
        static int depth[];
        static int binaryLifting[][];
        Tree(){
            binaryLifting=new int[n+1][20];
            depth=new int[n+1];
            dfs(1,0);
        }
        static int LCA(int a, int b) {
            if(depth[a]>depth[b]){
                int temp=a;
                a=b;
                b=temp;
            }
            // depth of a is less than depth of b
            b=jump(b,depth[b]-depth[a]);
            if(a==b){
                return a;
            }
            for(int i=HEIGHT-1;i>=0;i--){
                if(binaryLifting[a][i]!=binaryLifting[b][i]){
                    a=binaryLifting[a][i];
                    b=binaryLifting[b][i];
                }
            }
            return binaryLifting[a][0];
        }
        static int jump(int a, int height){
            int level=0;
            while(height!=0){
                if((height&1)==1){
                    a=binaryLifting[a][level];
                }
                level+=1;
                height=height>>1;
            }
            return a;
        }
        static void printBinaryLifting(){
            for(int i=0;i<=n;i++){
                System.out.print(i+" : ");
                for(int j=0;j<HEIGHT;j++){
                    System.out.print(binaryLifting[i][j]+" ");
                }
                System.out.println();
            }
        }
        static void printBinaryLiftingPeople(){
            for(int i=0;i<=n;i++){
                System.out.print(i+" : ");
                for(int j=0;j<HEIGHT;j++){
                    System.out.print(peopleBinaryLifting[i][j]+" ");
                }
                System.out.println();
            }
        }
        static boolean specialCase(ArrayList<Integer> mergeList){
            return mergeList.size()<10;
        }
        static ArrayList<Integer> combine(ArrayList<Integer> list1, ArrayList<Integer> list2){
            int i = 0, j = 0;
            ArrayList<Integer> mergedList=new ArrayList<>();
            if(list1==null)list1=new ArrayList<>();
            if(list2==null)list2=new ArrayList<>();
            while (i < list1.size() && j < list2.size() && specialCase(mergedList)) {
                if (list1.get(i) <= list2.get(j)) {
                    mergedList.add(list1.get(i));
                    i++;
                } else {
                    mergedList.add(list2.get(j));
                    j++;
                }
            }

            // Add remaining elements from the first list
            while (i < list1.size()&&specialCase(mergedList)) {
                mergedList.add(list1.get(i));
                i++;
            }

            // Add remaining elements from the second list
            while (j < list2.size()&&specialCase(mergedList)) {
                mergedList.add(list2.get(j));
                j++;
            }
            return mergedList;
        }
        static void dfs(int node, int par){
            binaryLifting[node][0]=par;
            depth[node]=depth[par]+1;
            peopleBinaryLifting[node][0]=new ArrayList<>(peopleAt[par]);
            for(int level=1;level<HEIGHT;level++){
                binaryLifting[node][level]=binaryLifting[binaryLifting[node][level-1]][level-1];
                peopleBinaryLifting[node][level]=new ArrayList<>(combine(peopleBinaryLifting[node][level-1],peopleBinaryLifting[binaryLifting[node][level-1]][level-1]));
            }
            for(int childNode:tree[node]){
                if(childNode==par)continue;
                dfs(childNode, node);
            }
        }
    }
}

```
</details>

### Day 4: April 5, 2024

**Today's Progress**: 
Solved the below three algorithmic problems: 
1. [Planet Queries 2](https://cses.fi/problemset/task/1160)
2. [MaxFlow](https://usaco.org/index.php?page=viewproblem2&cpid=576)
3. [Cyclic Array](https://cses.fi/problemset/task/1191/)

**Thoughts**: 
<details>
  <summary>Planet Queries 2</summary>
Well, this question, looks simple, to find the distance of travel, from planet a to b, if it ain't possible, then, print -1. When you apply brute force, you will soon understand that you need ancestor, and thereby if you are a bit familiar with Binary Jumping Alogirithm, you can clearly see the use case here. However, the trick was, for the cycles, that exist, how can we navigate that stuff ? Turns out, its not that hard, if you try artificially on paper, creating the binary jumping matrix, for a graph with trees as well as cycles. A simple implementation of binary jumping solves the problem. After this, I faced another error, what if, in the cycle, the point a is in front of point b, then I have to travel back to point b, for which, I have to take the length of cycle and substract the distance. Well, it took me long time to figure it out, it would not take so much time, if you are familiar with solving graph problems. Overall, this was a nice exercise, to wrap my head around functional graphs and directional graphs with trees and cycles.
  <details>
    <summary>code</summary>

  ```java

import java.io.*;
import java.util.StringTokenizer;

public class PlanetQueries2 {
    static Kattio x=new Kattio();
    static PrintWriter pw=new PrintWriter(System.out);
    static int n;
    static int q;
    static int arr[];
    static int binaryLifting[][];
    static int len[];

    public static void main(String[] args) {
        getInputForPresprocessing();
        binaryLifting=new int[n+1][20];
        len=new int[n+1];
        preprocess();
        query();
        pw.close();
    }
    static void preprocess(){
        for(int i=1;i<=n;i++){
            binaryLifting[i][0]=arr[i];
        }
        boolean visited[]=new boolean[n+1];
        for(int i=1;i<=n;i++){
            if(!visited[i]){
                dfs(i,visited);
            }
        }
    }
    static void dfs(int node, boolean visited[]){
        if(visited[node]){
            return;
        }
        visited[node]=true;
        dfs(arr[node],visited);
        len[node]=len[binaryLifting[node][0]]+1;
        for(int level=1;level<20;level++){
            binaryLifting[node][level]=binaryLifting[binaryLifting[node][level-1]][level-1];
        }
    }
    static void query(){
        StringBuilder output=new StringBuilder();
        for(int i=0;i<q;i++){
            int a=x.nextInt();
            int b=x.nextInt();
            int aa=jump(a,len[a]);  // in case, a is in front of b
            if(jump(a,len[a]-len[b])==b){
                pw.println(len[a]-len[b]);
            }
            else if(jump(aa,len[aa]-len[b])==b){
                pw.println((len[aa]-len[b])+len[a]);
            }
            else{
                pw.println(-1);
            }
        }
    }
    static int jump(int a, int dist){
        if(dist<0)return -1;
        int level=0;
        while(dist!=0){
            if((dist&1)==1){
                a=binaryLifting[a][level];
            }
            ++level;
            dist=dist>>1;
        }
        return a;
    }
    static void getInputForPresprocessing(){
        n=x.nextInt();
        q=x.nextInt();
        arr=new int[n+1];
        for(int i=1;i<=n;i++){
            arr[i]=x.nextInt();
        }
    }

    static class Kattio extends PrintWriter {
        private BufferedReader r;
        private StringTokenizer st;
        // standard input
        public Kattio() { this(System.in, System.out); }
        public Kattio(InputStream i, OutputStream o) {
            super(o);
            r = new BufferedReader(new InputStreamReader(i));
        }
        // USACO-style file input
        public Kattio(String problemName) throws IOException {
            super(problemName + ".out");
            r = new BufferedReader(new FileReader(problemName + ".in"));
        }
        // returns null if no more input
        public String next() {
            try {
                while (st == null || !st.hasMoreTokens())
                    st = new StringTokenizer(r.readLine());
                return st.nextToken();
            } catch (Exception e) {}
            return null;
        }
        public int nextInt() { return Integer.parseInt(next()); }
        public double nextDouble() { return Double.parseDouble(next()); }
        public long nextLong() { return Long.parseLong(next()); }
    }
}

```
    
  </details>
</details>
<details>
  <summary>MaxFlow</summary>
  Well, this was a challenging problem, took me some time to understand and then implement it. First, started with the the bruteforce way, simple, which shows quickly, that you need binary jumping in order to find lca or go the the kth Ancestor of a node, in a tree. However, the question was a bit unique, because, you needed some kind of a prefix sum implementation, since, milk flowing from a to b, goes through lca of a and b too, while doing the final addition in prefix sum, we have to make sure, not to add this value, to the parent of lca. This was a bit challenging to wrap heads around. However, once you start implementing, it becomes quite easy. The prefix sum can be done in two ways, either do a bfs and store the traversal in array, or do a dfs and do the prefix sum, as tail recursion. The later one was actually ingenious, which I found in the editoral. It didn't cross my mind, as to how elegent this method was.!
  <details>
    <summary>code with bfs</summary>

  ```java

import java.io.*;
import java.util.*;

public class MaxFlow2 {
    static PrintWriter pw;
    static BufferedReader x;
    static int MAX=50001;
    static ArrayList<Integer> tree[]=new ArrayList[MAX];
    static int HEIGHT=(int)(Math.log(MAX)/Math.log(2))+1;
    static int binaryLifting[][]=new int[MAX][HEIGHT];
    static int depth[]=new int[MAX];
    static ArrayList<Integer> breadFirstOrder=new ArrayList<>();
    static Tree T;
    static ArrayList<Integer> childNodes=new ArrayList<>();
    static int n;
    static void ininitalize(){
        x=new BufferedReader(new InputStreamReader(System.in));
        pw=new PrintWriter(System.out);
    }
    public static void main(String[] args) throws IOException {
        x=new BufferedReader(new FileReader("maxflow.in"));
        pw=new PrintWriter("maxflow.out");
//        ininitalize();
        StringTokenizer st=new StringTokenizer(x.readLine());
        int N=Integer.parseInt(st.nextToken());
        n=N;
        int K=Integer.parseInt(st.nextToken());
        for(int i=0;i<MAX;i++){
            tree[i]=new ArrayList<>();
        }
        for(int i=0;i<N-1;i++){
            st=new StringTokenizer(x.readLine());
            int a=Integer.parseInt(st.nextToken());
            int b=Integer.parseInt(st.nextToken());
            tree[a].add(b);
            tree[b].add(a);
        }
        T=new Tree();
        int ans[]=new int[N+1];
        for(int i=0;i<K;i++){
            st=new StringTokenizer(x.readLine());
            int a=Integer.parseInt(st.nextToken());
            int b=Integer.parseInt(st.nextToken());
            int lca=T.LCA(a,b);
            --ans[binaryLifting[lca][0]];
            --ans[lca];
            ++ans[a];
            ++ans[b];
        }
        for(int i=breadFirstOrder.size()-1;i>=0;i--){
            int node=breadFirstOrder.get(i);
            int parent=binaryLifting[node][0];
            ans[parent]+=ans[node];
        }
        int max=0;
        for(int i:ans)max=Math.max(i,max);
        pw.println(max);
        pw.close();
    }
    static class Tree extends MaxFlow2 {
        Tree(){
            dfs(1,0);
            bfs();
        }
        static void bfs(){
            Queue<Integer> q=new LinkedList<>();
            q.add(1);
            while(!q.isEmpty()){
                int node=q.poll();
                breadFirstOrder.add(node);
                for(int child:tree[node]){
                    if(binaryLifting[child][0]==node)
                        q.add(child);
                }
            }
        }
        static int LCA(int a, int b) {
            if(depth[a]>depth[b]){
                int temp=a;
                a=b;
                b=temp;
            }
            // depth of a is less than depth of b
            b=jump(b,depth[b]-depth[a]);
            if(a==b){
                return a;
            }
            for(int i=HEIGHT-1;i>=0;i--){
                if(binaryLifting[a][i]!=binaryLifting[b][i]){
                    a=binaryLifting[a][i];
                    b=binaryLifting[b][i];
                }
            }
            return binaryLifting[a][0];
        }
        static int jump(int a, int height){
            int level=0;
            while(height!=0){
                if((height&1)==1){
                    a=binaryLifting[a][level];
                }
                level+=1;
                height=height>>1;
            }
            return a;
        }
        static void printBinaryLifting(){
            for(int i=0;i<=n;i++){
                System.out.print(i+" : ");
                for(int j=0;j<HEIGHT;j++){
                    System.out.print(binaryLifting[i][j]+" ");
                }
                System.out.println();
            }
        }
        static void binaryLifting(){
            for(int i=1;i<MAX;i++){
                for(int level=1;level<HEIGHT;level++){
                    binaryLifting[i][level]=binaryLifting[binaryLifting[i][level-1]][level-1];
                }
            }
        }
        static void dfs(int node, int par){
            binaryLifting[node][0]=par;
            depth[node]=depth[par]+1;
            if(tree[node].size()==1){
                childNodes.add(node);
            }
            for(int level=1;level<HEIGHT;level++){
                binaryLifting[node][level]=binaryLifting[binaryLifting[node][level-1]][level-1];
            }
            for(int childNode:tree[node]){
                if(childNode==par)continue;
                dfs(childNode, node);
            }
        }
    }
}

  ```

  </details>
  <details>
    <summary>code without bfs</summary>

  ```java

import java.io.*;
import java.util.ArrayList;
import java.util.LinkedList;
import java.util.Queue;
import java.util.StringTokenizer;

public class MaxFlow1 {
    static BufferedReader x;
    static PrintWriter pw;
    static int MAX=50001;
    static ArrayList<Integer> tree[]=new ArrayList[MAX];
    static int n;
    static int HEIGHT=(int)(Math.log(MAX)/Math.log(2))+1;
    static int binaryLifting[][]=new int[MAX][HEIGHT];
    static int depth[]=new int[MAX];
    static Tree T;
    static int S[];
    static int E[];
    static int ans[];

    public static void main(String[] args)throws IOException {
        x=new BufferedReader(new InputStreamReader(System.in));
        pw=new PrintWriter(System.out);
        initializeInput();
        StringTokenizer st=new StringTokenizer(x.readLine());
        int N=Integer.parseInt(st.nextToken());
        n=N;
        S=new int[n+1];
        E=new int[n+1];
        ans=new int[n+1];
        int K=Integer.parseInt(st.nextToken());
        for(int i=0;i<=N;i++){
            tree[i]=new ArrayList<>();
        }
        for(int i=0;i<N-1;i++){
            st=new StringTokenizer(x.readLine());
            int a=Integer.parseInt(st.nextToken());
            int b=Integer.parseInt(st.nextToken());
            tree[a].add(b);
            tree[b].add(a);
        }
        T=new Tree();
        for(int i=0;i<K;i++){
            st=new StringTokenizer(x.readLine());
            int a=Integer.parseInt(st.nextToken());
            int b=Integer.parseInt(st.nextToken());
            int lca=T.LCA(a,b);
            ++ans[a];
            ++ans[b];
            --ans[lca];
            --ans[binaryLifting[lca][0]];
            ++S[a];
            ++S[b];
            ++E[lca];
        }
        /*
         * below are two methods, solve and solve1, both of them work
         * solve1: don't requires in edge and out edge arrays, only the ans array is sufficient
         */
//        solve(1,0);
        solve1(1,0);
        int max=0;
        for(int i:ans)max=Math.max(i,max);
        pw.println(max);
        pw.close();
    }
    static int solve(int node, int parent){
        int sumOfChildNodesOfCurrentNode=0;
        for(int child:tree[node]){
            if(parent!=child){
                sumOfChildNodesOfCurrentNode+=solve(child,node);
            }
        }
        int sumAtCurrentNode=sumOfChildNodesOfCurrentNode+S[node]-E[node];
        ans[node]=sumAtCurrentNode;
        return sumAtCurrentNode-E[node];
    }
    static int solve1(int node, int parent){
        for(int child:tree[node]){
            if(parent!=child){
                ans[node]+=solve1(child,node);
            }
        }
        return ans[node];
    }
    static void initializeInput()throws IOException {
        String in="maxflow";
        x=new BufferedReader(new FileReader(in+".in"));
        pw=new PrintWriter(in+".out");
    }
    static class Tree extends MaxFlow1 {
        Tree(){
            dfs(1,0);
        }
        static int LCA(int a, int b) {
            if(depth[a]>depth[b]){
                int temp=a;
                a=b;
                b=temp;
            }
            // depth of a is less than depth of b
            b=jump(b,depth[b]-depth[a]);
            if(a==b){
                return a;
            }
            for(int i=HEIGHT-1;i>=0;i--){
                if(binaryLifting[a][i]!=binaryLifting[b][i]){
                    a=binaryLifting[a][i];
                    b=binaryLifting[b][i];
                }
            }
            return binaryLifting[a][0];
        }
        static int jump(int a, int height){
            int level=0;
            while(height!=0){
                if((height&1)==1){
                    a=binaryLifting[a][level];
                }
                level+=1;
                height=height>>1;
            }
            return a;
        }
        static void printBinaryLifting(){
            for(int i=0;i<=n;i++){
                System.out.print(i+" : ");
                for(int j=0;j<HEIGHT;j++){
                    System.out.print(binaryLifting[i][j]+" ");
                }
                System.out.println();
            }
        }
        static void binaryLifting(){
            for(int i=1;i<MAX;i++){
                for(int level=1;level<HEIGHT;level++){
                    binaryLifting[i][level]=binaryLifting[binaryLifting[i][level-1]][level-1];
                }
            }
        }
        static void dfs(int node, int par){
            binaryLifting[node][0]=par;
            depth[node]=depth[par]+1;
            for(int level=1;level<HEIGHT;level++){
                binaryLifting[node][level]=binaryLifting[binaryLifting[node][level-1]][level-1];
            }
            for(int childNode:tree[node]){
                if(childNode==par)continue;
                dfs(childNode, node);
            }
        }
    }
}

```
  </details>
</details>
<details>
  <summary>Cyclic Array</summary>
Ngl, this problem fried my brains. It was, like mind expanding shit. It was equivalent to doing "legs" in the gym. I tried a lot of ways of solving this problem, but failed. Until, I gained an insight, to see it as a graph problem. Then, create a binary lifting matrix. After this, create a binarylifting matrix for the length of the subarray. After this, this is the genius part. If you observe, the highest no. of subarrays, are the no. of elements of the arrays, you can't exceed that, same for the lowest no. of subarray, which is one, you can't have less than that (well, or 0). So, why don't we binary search the solution !!! So what we will do is, guess a solution, check this solution, for all the subarrays starting from 0, 1, 2…..n. if the no of elements inside the subarray, exceeds or are same as the size of the array, then that solution is feasible, else it's not. It might be a bit complex to undestand at first, I don’t blame you, it kind of grows on you. Also, I am not an expert in writing editorials :-(
  <details>
    <summary>code</summary>

  ```java

import java.io.DataInputStream;
import java.io.FileInputStream;
import java.io.IOException;
import java.util.Arrays;

public class CyclicArray1 {
    static FastReader x=new FastReader();
    static int n;
    static long length[][];
    static int binaryLifting[][];
    public static void main(String[] args) {
        System.out.println(solve());
    }
    static int solve(){
        n=x.nextInt();
        long k=x.nextLong();
        int arr[]=new int[n];
        for(int i=0;i<n;i++)arr[i]=x.nextInt();
        binaryLifting=new int[n][20];
        length=new long[n][20];
        int r=0;long sum=0;
        for(int i:arr)sum+=i;
        if(sum<=k)return 1;
        sum=0;
        for(int i=0;i<n;i++){
            while(arr[r%n]+sum<=k){
                sum+=arr[r%n];
                ++r;
            }
            sum=sum-arr[i];
            binaryLifting[i][0]=r%n;
            length[i][0]=(r-i);
        }
        for(int level=1;level<20;level++){
            for(int i=0;i<n;i++){
                binaryLifting[i][level]=binaryLifting[binaryLifting[i][level-1]][level-1];
                length[i][level]=length[i][level-1]+length[binaryLifting[i][level-1]][level-1];
            }
        }
        for(int i=0;i<20;i++){
            System.out.println(i+" "+ Arrays.toString(length[i]));
        }
        int l=1; r=n;
        while(l<=r){
            int mid=((l+r)/2);
            if(check(mid)){
                r=mid-1;
            }
            else{
                l=mid+1;
            }
        }
        return l;
    }
    static boolean check(int size){
        for(int i=0;i<n;i++){
            int temp=size, node=i, level=0;
            long len=0;
            while(temp>0){
                if((temp&1)==1){
                    len+=length[node][level];
                    node=binaryLifting[node][level];
                }
                ++level;
                temp=temp>>1;
            }
            if(len>=n){
                return true;
            }
        }
        return false;
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


  ```
  </details>
  <details>
    <summary>a small view, of my experiments</summary>

  ```java

import java.io.DataInputStream;
import java.io.FileInputStream;
import java.io.IOException;
import java.util.*;

public class CyclicArray {
    static int c=-1;
    static FastReader x=new FastReader();
    public static void main(String[] args) {
//        System.out.println(method1());
//        System.out.println(method3());
        System.out.println(method4());
    }
    static int method3(){
        int n=x.nextInt();
        int k=x.nextInt();
        int arr[]=new int[n];
        for(int i=0;i<n;i++){
            arr[i]=x.nextInt();
        }
        int sum=0, r=0;
        int series[]=new int[n];
        for(int l=0;l<n;l++){
            while(sum+arr[r%n]<=k){
                sum+=arr[r%n];
                ++r;
            }
            sum-=arr[l];
            series[l]=r%n;
        }
        System.out.println(Arrays.toString(series));
        int ans=Integer.MAX_VALUE;
        for(int i=0;i<n;i++){
            int cnt=0, ci=i;
            System.out.println();
            while(ci>=i){
                System.out.println(ci);
                ci=series[ci];
                ++cnt;
            }
            ans=Math.min(ans,cnt);
        }
        return ans;
    }
    static int method4(){
        int n=x.nextInt();
        long k=x.nextLong();
        int arr[]=new int[n];
        for(int i=0;i<n;i++)arr[i]=x.nextInt();
        int binaryLifting[][]=new int[n][20];
        long length[][]=new long[n][20];
        int r=0;long sum=0;
        for(int i:arr)sum+=i;
        if(sum<=k)return 1;
        sum=0;
        for(int i=0;i<n;i++){
            while(arr[r%n]+sum<=k){
                sum+=arr[r%n];
                ++r;
            }
            sum=sum-arr[i];
            binaryLifting[i][0]=r%n;
            length[i][0]=(r-i);
        }
        for(int level=1;level<20;level++){
            for(int i=0;i<n;i++){
                binaryLifting[i][level]=binaryLifting[binaryLifting[i][level-1]][level-1];
                length[i][level]=length[i][level-1]+length[binaryLifting[i][level-1]][level-1];
            }
        }
        class HelperMethod{
            long totalNoOfElements(int startIndex, int noOfSubarrays){
                int level=0;long noOfElements=0;
                while(noOfSubarrays!=0){
                    if((noOfSubarrays&1)==1){
                        noOfElements+=length[startIndex][level];
                        startIndex=binaryLifting[startIndex][level];
                    }
                    ++level;
                     noOfSubarrays=noOfSubarrays>>1;
                 }
                return noOfElements;
            }
            boolean check(int x){
                for(int i=0;i<n;i++){
                    if(totalNoOfElements(i,x)>=n)return true;
                }
                return false;
            }
            void print(int arr[][]){
                System.out.println();
                int c=0;
                for(int i[]:arr){
                    System.out.print(c+++": ");
                    for(int j:i){
                        System.out.print(j+" ");
                    }
                    System.out.println();
                }
            }
        }
        int l=0; r=n;
        HelperMethod h=new HelperMethod();
//        h.print(binaryLifting);
//        h.print(length);
        while(l<r-1){
            int mid=(l+r)/2;
            if(h.check(mid)){
                r=mid;
            }
            else{
//                System.out.println(mid);
                l=mid;
            }
        }
        return r;
    }
    static int method2(){
        class Helper implements Comparator<ArrayList<Integer>> {
            public int compare(ArrayList<Integer> arr1, ArrayList<Integer> arr2)
            {
                // using compareTo() to ensure
                return arr1.get(0).compareTo(arr2.get(0));
            }
        }
        int n=x.nextInt();
        int k=x.nextInt();
        int arr[]=new int[n];
        for(int i=0;i<n;i++){
            arr[i]=x.nextInt();
        }
        TreeSet<ArrayList<Integer>> tm=new TreeSet<>(new Helper());
        int sum=0, r=0;
        for(int i=0;i<n;i++){
            while(sum+arr[r%n]<=k){
                sum+=arr[r%n];
                ++r;
                System.out.println(sum);
            }
            ArrayList<Integer> temp=new ArrayList<>();
            temp.add(i);
            temp.add(r%n);
            tm.add(temp);
            sum-=arr[i];
        }
        System.out.println(tm);
        return 0;
    }

    static int method1(){
        class method{
            int getAnswer(long k, Queue<Integer> q){
                long sum=0; int ans=0;
                ++c;
                int tempc=c;
                ArrayList<Integer> arr2=new ArrayList<>();
                ArrayList<Integer> arr=new ArrayList<>();
                arr2.add(c);
                for(int i:q){
                    if(sum+i>k){
                        arr2.add(tempc%q.size());
                        System.out.print(arr);
                        arr=new ArrayList<>();
                        sum=0;
                        ++ans;
                    }
                    arr.add(i);
                    ++tempc;
                    sum+=i;
                }
                System.out.print(arr+" ");
                for(int i:arr2){
                    System.out.print(i+"->");
                }
                if(sum!=0){
                    ++ans;
                }
                return ans;
            }
        }
        int n=x.nextInt();
        long k=x.nextLong();
        Queue<Integer> q=new LinkedList<>();
        for(int i=0;i<n;i++){
            int temp=x.nextInt();
            q.add(temp);
        }
        int ans=Integer.MAX_VALUE;
        for(int i=0;i<n;i++){
            System.out.println();
            ans=Math.min(ans, new method().getAnswer(k,q));
            q.add(q.poll());
        }
        return ans;
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

```

  </details>
</details>

### Day 5: April 6, 2024

**Today's Progress**: 
Solved the below three algorithmic problems: 
1. [The Bovine Shuffle](https://usaco.org/index.php?page=viewproblem2&cpid=764)
2. [Leetcode: Minimim Edge Weights Equilibrium Queries in a Tree](https://leetcode.com/problems/minimum-edge-weight-equilibrium-queries-in-a-tree/description/)

**Thoughts**: 
<details>
  <summary>The Bovine Shuffle</summary>
It’s a problem, complex to understand, however, once you understand it, and have some familiarity with functional graphs and floyd's algorithm, implementation is a breeze. Basically, the answer is sum of all the cycle sizes. Because, those are the positions, which will never be empty after a single shift or n no. of shifts. Any positions, not in a cycle, will get empty if there is atleast 1 shift.
<details>
<summary>Implementation:</summary>
  
```java

					importjava.io.*;
					importjava.util.HashSet;
					
					publicclassTheBovineShuffle{
					staticINPUT_TYPEUSACO=INPUT_TYPE.USACO;
					staticINPUT_TYPELOCAL=INPUT_TYPE.LOCAL;
					staticINPUT_TYPELOCAL_CMD=INPUT_TYPE.LOCAL_COMMANDLINE;
					staticINPUT_TYPEinputType;
					staticintnoOfInputs=0;
					staticStringproblemName="shuffle";
					staticFastReaderx=newFastReader();
					staticPrintWriterpw=newPrintWriter(System.out);
					
					publicstaticvoidmain(String[]args)throwsIOException{
					inputType=INPUT_TYPE.USACO;
					//inputType=INPUT_TYPE.LOCAL_COMMANDLINE;
					noOfInputs=1;
					if(inputType==INPUT_TYPE.USACO){
					x=newFastReader(problemName+".in");
					pw=newPrintWriter(problemName+".out");
					getAnswer();
					}
					elseif(inputType==INPUT_TYPE.LOCAL){
					x=newFastReader("D:\\usaco.guide\\FunctionalGraph\\"+problemName+".in");
					for(inti=0;i<noOfInputs;i++){
					getAnswer();
					}
					}
					elseif(inputType==INPUT_TYPE.LOCAL_COMMANDLINE){
					getAnswer();
					}
					pw.close();
					}
					staticvoidgetAnswer(){
					getAnswer3();
					}
					staticvoidgetAnswer1(){
					intN=x.nextInt();
					intarr[]=newint[N+1];
					for(inti=1;i<=N;i++){
					arr[i]=x.nextInt();
					}
					HashSet<Integer>visited=newHashSet<>();
					classmethod{
					inta,b;
					intfloyd(intx){
					a=arr[x];
					b=arr[arr[x]];
					while(a!=b){
					a=arr[a];
					b=arr[arr[b]];
					}
					a=x;
					while(a!=b){
					visited.add(a);
					a=arr[a];
					b=arr[b];
					}
					intfirst=a,length=0;
					if(visited.contains(first))return0;
					b=first;
					do{
					visited.add(b);
					b=arr[b];
					++length;
					}while(first!=b);
					returnlength;
					}
					}
					intans=0;
					methodm=newmethod();
					for(inti=1;i<=N;i++){
					if(!visited.contains(i)){
					ans+=m.floyd(i);
					}
					}
					pw.println(ans);
					}
					staticvoidgetAnswer2(){
					intN=x.nextInt();
					intarr[]=newint[N+1];
					for(inti=1;i<=N;i++){
					arr[i]=x.nextInt();
					}
					HashSet<Integer>visited=newHashSet<>();
					classmethod{
					inta,b;
					intfloyd(intx){
					a=arr[x];
					b=arr[arr[x]];
					while(a!=b){
					a=arr[a];
					b=arr[arr[b]];
					}
					a=x;
					if(visited.contains(b))return0;
					while(a!=b){
					a=arr[a];
					b=arr[b];
					}
					intfirst=a,length=0;
					b=first;
					do{
					b=arr[b];
					++length;
					}while(first!=b);
					a=x;
					while(a!=first){
					visited.add(a);
					a=arr[a];
					}
					b=first;
					do{
					visited.add(b);
					b=arr[b];
					}while(b!=first);
					returnlength;
					}
					}
					intans=0;
					methodm=newmethod();
					for(inti=1;i<=N;i++){
					if(!visited.contains(i)){
					ans+=m.floyd(i);
					}
					}
					pw.println(ans);
					}
					staticvoidgetAnswer3(){
					intN=x.nextInt();
					intarr[]=newint[N+1];
					for(inti=1;i<=N;i++){
					arr[i]=x.nextInt();
					}
					intkyc[]=newint[N+1];
					HashSet<Integer>visited=newHashSet<>();
					classmethod{
					inta,b;
					intfloyd(intx){
					a=arr[x];
					b=arr[arr[x]];
					while(a!=b){
					if(visited.contains(a))return0;
					a=arr[a];
					b=arr[arr[b]];
					}
					if(visited.contains(b))return0;
					a=x;
					while(a!=b){
					a=arr[a];
					b=arr[b];
					}
					intfirst=a,length=0;
					b=first;
					do{
					visited.add(b);
					b=arr[b];
					++length;
					}while(first!=b);
					returnlength;
					}
					}
					classmethod2{
					intfloyd(intx){
					inta=arr[x];
					intb=arr[arr[x]];
					while(a!=b){
					visited.add(a);
					a=arr[a];
					b=arr[arr[b]];
					}
					a=x;
					while(a!=b){
					a=arr[a];
					b=arr[b];
					}
					intfirst=a,length=0;
					b=first;
					do{
					kyc[b]=2;
					b=arr[b];
					++length;
					}while(first!=b);
					returnlength;
					}
					}
					intans=0;
					methodm=newmethod();
					for(inti=1;i<=N;i++){
					if(!visited.contains(i)){
					ans+=m.floyd(i);
					}
					}
					/*
					Justananotherwayofsolvingtheproblem.nobigdeal
					
					method2m2=newmethod2();
					for(inti=1;i<=N;i++){
					if(!visited.contains(i)){
					m2.floyd(i);
					}
					}
					for(inti:kyc){
					if(i==2){
					++ans;
					}
					}
					*/
					pw.println(ans);
					}
					enumINPUT_TYPE{
					USACO,LOCAL,LOCAL_COMMANDLINE;
					}
					staticclassFastReader{
					finalprivateintBUFFER_SIZE=1<<16;
					privateDataInputStreamdin;
					privatebyte[]buffer;
					privateintbufferPointer,bytesRead;
					
					publicFastReader(){
					din=newDataInputStream(System.in);
					buffer=newbyte[BUFFER_SIZE];
					bufferPointer=bytesRead=0;
					}
					
					publicFastReader(Stringfile_name)throwsIOException{
					din=newDataInputStream(newFileInputStream(file_name));
					buffer=newbyte[BUFFER_SIZE];
					bufferPointer=bytesRead=0;
					}
					
					publicStringnextLine(){
					try{
					byte[]buf=newbyte[10000000];//linelength
					intcnt=0,c;
					while((c=read())!=-1){
					if(c=='\n')
					break;
					buf[cnt++]=(byte)c;
					}
					returnnewString(buf,0,cnt);
					}catch(Exceptione){
					System.out.println(e.getMessage());
					returnnull;
					}
					}
					
					publicintnextInt(){
					intret=0;
					try{
					bytec=read();
					while(c<='')
					c=read();
					booleanneg=(c=='-');
					if(neg)c=read();
					do{
					ret=ret*10+c-'0';
					}while((c=read())>='0'&&c<='9');
					
					if(neg)return-ret;
					returnret;
					}catch(Exceptione){
					System.out.println(e.getMessage());
					return-1;
					}
					}
					
					publiclongnextLong(){
					
					try{
					longret=0;
					bytec=read();
					while(c<='')c=read();
					booleanneg=(c=='-');
					if(neg)
					c=read();
					do{
					ret=ret*10+c-'0';
					}
					while((c=read())>='0'&&c<='9');
					if(neg)
					return-ret;
					returnret;
					}catch(Exceptione){
					System.out.println(e.getMessage());
					return-1;
					}
					}
					
					publicdoublenextDouble(){
					
					try{
					doubleret=0,div=1;
					bytec=read();
					while(c<='')
					c=read();
					booleanneg=(c=='-');
					if(neg)c=read();
					
					do{
					ret=ret*10+c-'0';
					}
					while((c=read())>='0'&&c<='9');
					if(c=='.'){
					while((c=read())>='0'&&c<='9'){
					ret+=(c-'0')/(div*=10);
					}
					}
					
					if(neg)return-ret;
					returnret;
					}catch(Exceptione){
					System.out.println(e.getMessage());
					return-1;
					}
					}
					
					privatevoidfillBuffer()throwsIOException{
					bytesRead=din.read(buffer,bufferPointer=0,BUFFER_SIZE);
					if(bytesRead==-1)
					buffer[0]=-1;
					}
					
					privatebyteread()throwsIOException{
					try{
					if(bufferPointer==bytesRead)
					fillBuffer();
					returnbuffer[bufferPointer++];
					}catch(Exceptione){
					System.out.println(e.getMessage());
					return-1;
					}
					}
					
					publicvoidclose()throwsIOException{
					if(din==null)
					return;
					din.close();
					}
					}
					}
					enumINPUT_TYPE{
					USACO,LOCAL,LOCAL_COMMANDLINE;
					}

```
  
</details>
</details>
<details>
  <summary>Minimim Edge Weights Equilibrium Queries in a Tree:</summary>
				If you are familiar with binary jumping, then you can tell it right away, becase there are two nodes given, and you need to find the path between the two nodes, and to find the path, you ougth to find the lca of those two nodes. Thereby, lca, means, binary lifting.
				If you see the constraints, you will notice, the edge weights are constrained at 26, for which you can take a constant array of that size. The use binary lifting to create a matrix, which contains the frequency of the edge weights in respective paths. Then, similar to how you find, the lca, find out the frequency of edge weights in those paths.
				If you wanna optimize even further, then, instead of using array, use bitwise operator, and xor, it will be faster.

```java
				class Solution {
				    static ArrayList<Integer> tree[];
				    // static int edgeWeight[][][];
				    static int edgeWeightLifting[][][];
				    static HashMap<ArrayList<Integer>,int[]> edgewt;
				    static int depth[]; 
				    static Tree t;
				    static int n;
				    public int[] minOperationsQueries(int n1, int[][] edges, int[][] queries) {
				        n=n1;        
				        tree=new ArrayList[n+1];
				        // edgeWeight=new int[n][n][27];
				        edgewt=new HashMap<>();
				        edgeWeightLifting=new int[n][14][27];
				        for(int i=0;i<=n;i++)tree[i]=new ArrayList<Integer>();
				        for(int i[]:edges){
				            tree[i[0]].add(i[1]);
				            tree[i[1]].add(i[0]);
				            addEdgeWeight(i);
				        }
				        t=new Tree();
				        // t.printBinaryLifting();
				        // t.printEdgeLifting();
				        int answer[]=new int[queries.length];
				        int c=0;
				        for(int q[]:queries){
				            answer[c++]=getOutput(q);
				        }
				        return answer;
				    }
				    static void addEdgeWeight(int i[]){
				        // ++edgeWeight[i[0]][i[1]][i[2]];
				        // ++edgeWeight[i[1]][i[0]][i[2]];
				        ArrayList<Integer> temp=new ArrayList<Integer>();
				        temp.add(i[0]);
				        temp.add(i[1]);
				        if(!edgewt.containsKey(temp)){
				            edgewt.put(temp,new int[27]);
				        }
				        ++edgewt.getOrDefault(temp,new int[27])[i[2]];
				    }
				    static int getOutput(int q[]){
				        int a=q[0];
				        int b=q[1];
				        if(t.depth[a]<t.depth[b]){
				            int temp=a;
				            a=b;
				            b=temp;
				        }
				        int dif=t.depth[a]-t.depth[b];
				        int level=0;
				        int hm[]=t.initialize();
				        int hm_a[]=t.initialize();
				        int hm_b[]=t.initialize();
				        while(dif>0){
				            if((dif&1)==1){
				                t.add(hm_a,edgeWeightLifting[a][level]);
				                a=t.binaryLifting[a][level];
				            }
				            ++level;
				            dif=dif>>1;
				        }
				        if(a==b){
				            t.add(hm,hm_a);
				        }
				        else{
				            // System.out.println(a+" "+b+" "+Arrays.toString(hm_a));
				            for(int i=t.HEIGHT-1;i>=0;i--){
				                if(t.binaryLifting[a][i]!=t.binaryLifting[b][i]){
				                // if(a!=b){
				                    t.add(hm_a,edgeWeightLifting[a][i]);
				                    t.add(hm_b,edgeWeightLifting[b][i]);
				                    a=t.binaryLifting[a][i];
				                    b=t.binaryLifting[b][i];
				                }
				            }
				            t.add(hm_a,getEdgeWeight(a,t.binaryLifting[a][0]));
				            t.add(hm_b,getEdgeWeight(b,t.binaryLifting[b][0]));
				            t.add(hm,hm_a);
				            t.add(hm,hm_b);
				        }
				        // System.out.println(q[0]+" "+q[1]+" "+Arrays.toString(hm));
				        return getResult(hm);
				    }
				    static int[] getEdgeWeight(int a, int b){
				        // return edgeWeight[a][b];
				        ArrayList<Integer> temp1=new ArrayList<Integer>();
				        ArrayList<Integer> temp2=new ArrayList<Integer>();
				        temp1.add(a);temp1.add(b);
				        temp2.add(b);temp2.add(a);
				        if(edgewt.containsKey(temp1))return edgewt.get(temp1);
				        else return edgewt.get(temp2);
				    }
				    static int getResult(int arr[]){
				        int sum=0,max=0;
				        for(int i:arr){
				            max=Math.max(max,i);
				            sum+=i;
				        }
				        return sum-max;
				    }
				    static class Tree extends Solution {
				            static int HEIGHT=14;
				            static int depth[];
				            // static int n=10000;
				            static int binaryLifting[][];
				            Tree(){
				                binaryLifting=new int[n+1][14];
				                for(int i[]:binaryLifting)Arrays.fill(i,-1);
				                depth=new int[n+1];
				                dfs(0,-1);
				            }
				            static int LCA(int a, int b) {
				                if(depth[a]>depth[b]){
				                    int temp=a;
				                    a=b;
				                    b=temp;
				                }
				                // depth of a is less than depth of b
				                b=jump(b,depth[b]-depth[a]);
				                if(a==b){
				                    return a;
				                }
				                for(int i=HEIGHT-1;i>=0;i--){
				                    if(binaryLifting[a][i]!=binaryLifting[b][i]){
				                        a=binaryLifting[a][i];
				                        b=binaryLifting[b][i];
				                    }
				                }
				                return binaryLifting[a][0];
				            }
				            static int jump(int a, int height){
				                int level=0;
				                while(height!=0){
				                    if((height&1)==1){
				                        a=binaryLifting[a][level];
				                    }
				                    level+=1;
				                    height=height>>1;
				                }
				                return a;
				            }
				            static void printBinaryLifting(){
				                System.out.println();
				                for(int i=0;i<=n;i++){
				                    System.out.print(i+" : ");
				                    for(int j=0;j<HEIGHT;j++){
				                        System.out.print(binaryLifting[i][j]+" ");
				                    }
				                    System.out.println();
				                }
				            }
				            static void printEdgeLifting(){
				                System.out.println();
				                for(int i=0;i<n;i++){
				                    System.out.print(i+" : ");
				                    for(int j=0;j<4;j++){
				                        System.out.print(Arrays.toString(edgeWeightLifting[i][j])+" ");
				                    }
				                    System.out.println();
				                }
				            }
				            static int[] initialize(){
				                int hm[]=new int[27];
				                return hm;
				            }
				            // static void add(int hm[], int node){
				            //     int n=edgeWeight[node]==-1?0:1;
				            //     ++hm[n];
				            // }
				            static void add(int hm[], int child[]){
				                for(int i=0;i<hm.length;i++){
				                    hm[i]+=child[i];
				                }
				            }
				            static void dfs(int node, int par){
				                binaryLifting[node][0]=par;
				                if(par!=-1){
				                    depth[node]=depth[par]+1;
				                    add(edgeWeightLifting[node][0],getEdgeWeight(node,par));
				                }
				                for(int level=1;level<HEIGHT;level++){
				                    if(binaryLifting[node][level-1]==-1)break;
				                    binaryLifting[node][level]=binaryLifting[binaryLifting[node][level-1]][level-1];
				                    if(binaryLifting[node][level]==-1)break;
				                    add(edgeWeightLifting[node][level],edgeWeightLifting[node][level-1]);
				                    add(edgeWeightLifting[node][level],edgeWeightLifting[binaryLifting[node][level-1]][level-1]);
				                }
				                for(int childNode:tree[node]){
				                    if(childNode==par)continue;
				                    dfs(childNode, node);
				                }
				            }
				        }
				}

```
</details>


### Day 6: April 7, 2024

**Today's Progress**: 
1. Gave leetcode virtual contest, however, haven't upsolved yet. Will provide updates about it, on the following days
2. Revised some prior problems, that I had solved. The problems are:
3. [Badge](https://codeforces.com/contest/1020/problem/B)
4. [Cooperative Games](https://codeforces.com/contest/1137/problem/D)
5. [Watching Mooloo](https://usaco.org/index.php?page=viewproblem2&cpid=1301)

**Thoughts**: 
<details>
	<summary>Badge</summary>
Its an interesting problem, which requires to read the question multiple times, to get a complete picture of the small intrecacies. The problem is essentially, a directional graph, with nodes of a single outdegree, thereby resulting in multiplie connected components with cycles. In order to get a clearer overview, its best, to draw out the test cases, helps in better visualisation. Anyways, if you are familiar with floyd's algo, its easy to figure out, the brute force approach is, call floyd's algo or dfs or any other graph traversal, over each node, and find out, which "visited" node is being visited again. However to optimize it, you need floyd's algo, and mark the nodes, which has been visited, and fill answer nodes, while running the floyd algo. Nodes, which ain't in cycle, will have answer as the first node, from where the cycle starts, and the nodes in cycle will have answer equaling their own node value. While running the floyd's algo, if you find any node, that's already visited, then if you have drawn such a test case, its clear that, for its answer, the answer of that node, which has already been visited, is the answer to this node.

```java

							import java.io.*;
							import java.util.*;
							
							public class Badge {
								static int[] adj;
								static int[] ans;
								/*
								 * For each node, we need a list to store its children; at nodes
								 * combining the cycle with other part of the connected component, there
								 * would be more than one outgoing arrow in the reversed adjacency list
								 */
								static List<List<Integer>> radj;
							
								public static void main(String[] args) throws IOException {
									BufferedReader in =
									    new BufferedReader(new InputStreamReader(System.in));
							
									int n = Integer.parseInt(in.readLine());
									adj = new int[n];
									ans = new int[n];
									radj = new ArrayList<>();
							
									StringTokenizer st = new StringTokenizer(in.readLine());
									for (int i = 0; i < n; i++) {
										adj[i] = Integer.parseInt(st.nextToken()) - 1;
										ans[i] = -1;
										radj.add(new ArrayList<>());
									}
							
									for (int i = 0; i < n; i++) { radj.get(adj[i]).add(i); }
							
									for (int i = 0; i < n; i++) {
										// run Floyd's algorithm on every connected component
										if (ans[i] == -1) { floyd(i); }
									}
							
									for (int i = 0; i < n; i++) { System.out.print(ans[i] + 1 + " "); }
								}
							
								private static void floyd(int x) {
									int a = adj[x];
									int b = adj[adj[x]];
							
									// find a cycle using Floyd's algorithm
									while (a != b) {
										a = adj[a];
										b = adj[adj[b]];
									}
							
									// for each node a in the cycle, the answer ans[a] will be a as well
									do {
										ans[a] = a;
										a = adj[a];
									} while (a != b);
							
									// for each node a that has outgoing arrow(s) pointing to the acyclic
									// part we set their answers with fillRadj
									do {
										fillRadj(a);
										a = adj[a];
									} while (a != b);
								}
							
								private static void fillRadj(int x) {
									for (int child : radj.get(x)) {
										/*
										 * As all nodes in the cycle are processed in method floyd, the
										 * recursive call will only start at the nodes which combine the
										 * cycle with the acyclic part of the connected component, where one
										 * of its outgoing arrows points to the node that is not processed
										 * yet
										 */
										if (ans[child] == -1) {
											ans[child] = ans[x];
											fillRadj(child);
										}
									}
								}
							}


```
</details>
<details>
	<summary>Cooperative Games</summary>
	It’s a very interesting and an important problem, which exponentially helped me, to understand the floyd's algorithm deeply. It initially, seems to be a big ass complex question, but trust me, its peanuts, if you "really" understand the floyd's algo. I would even say, this might be a litmus test, if you understand this algorithm or not. So, first of all, break the groups of friends into 3, let the two group of friends proceed one before the other, and second group getting one extra lead every time (something similar to fast pointer and slow pointer), until the two groups meet and form 1 group, in total there are two groups now. Now, run both the group and they will meet at the finish line (which is the first node, between the non-cycle and nodes in cycle). There is a really cool mathematical explanation, on why this happens, if you see this video, you will easily understand the video as well as the question, like, you can just visually see the solution. (<a href="https://www.youtube.com/watch?v=PvrxZaH_eZ4&ab_channel=Insidecode">Floyd's cycle detection algorithm (Tortoise and hare) - Inside code</a>)

 ```java
importjava.io.BufferedReader;
							importjava.io.IOException;
							importjava.io.InputStreamReader;
							importjava.util.StringTokenizer;
							
							publicclassCooperativeGame{
							staticBufferedReaderx=newBufferedReader(newInputStreamReader(System.in));
							publicstaticvoidmain(String[]args)throwsIOException{
							getAnswer1();
							//ignoreanswer2,wasjustexperimentingandshit
							//getAnswer2();
							}
							staticvoidgetAnswer1()throwsIOException{
							Stringa="012";
							Stringb="345";
							Stringc="6789";
							Stringspace="";
							Stringnext="next";
							classmethod{
							intmove(Stringx)throwsIOException{
							System.out.println(next+space+x);
							intgrp=Integer.parseInt(getInput().nextToken());
							returngrp;
							}
							StringTokenizergetInput()throwsIOException{
							StringTokenizerst=newStringTokenizer(x.readLine());
							returnst;
							}
							}
							methodm=newmethod();
							intgroup=3;
							while(group==3){
							m.move(a+space+b);
							group=m.move(b);
							}
							while(group==2){
							group=m.move(a+space+b+space+c);
							}
							System.out.println("done");
							}
							staticvoidgetAnswer2()throwsIOException{
							Stringa="012345";
							Stringb="6789";
							Stringspace="";
							Stringnext="next";
							classmethod{
							intmove(Stringx)throwsIOException{
							System.out.println(next+space+x);
							intgrp=Integer.parseInt(getInput().nextToken());
							returngrp;
							}
							StringTokenizergetInput()throwsIOException{
							StringTokenizerst=newStringTokenizer(x.readLine());
							returnst;
							}
							}
							
							methodm=newmethod();
							intgroup=m.move(b);
							while(group>1){
							m.move(a+space+b);
							group=m.move(b);
							}
							System.out.println("done");
							}
							}

```

</details>
<details>
	<summary>Watching Mooloo</summary>
	A really simple puzzle type problem, where, if you observe, for every step, essentially, we have to find, if buying a single day subscription is cheaper than buying a durational subscription, for each of the days, defined. The implementation, was a little bit tricky for me. It’s a unique problem in the sense, understanding the problem and arriving to the solution and understanding the math behind it was simple, however, implementation was where, the maximum time went. Had to incorporate a lot of print statements, in order to debug the errors!

```java

							importjava.io.*;
							importjava.util.*;
							
							classKattioextendsPrintWriter{
							privateBufferedReaderr;
							privateStringTokenizerst;
							//standardinput
							publicKattio(){this(System.in,System.out);}
							publicKattio(InputStreami,OutputStreamo){
							super(o);
							r=newBufferedReader(newInputStreamReader(i));
							}
							//USACO-stylefileinput
							publicKattio(StringproblemName)throwsIOException{
							super(problemName+".out");
							r=newBufferedReader(newFileReader(problemName+".in"));
							}
							//returnsnullifnomoreinput
							publicStringnext(){
							try{
							while(st==null||!st.hasMoreTokens())
							st=newStringTokenizer(r.readLine());
							returnst.nextToken();
							}catch(Exceptione){}
							returnnull;
							}
							publicintnextInt(){returnInteger.parseInt(next());}
							publicdoublenextDouble(){returnDouble.parseDouble(next());}
							publiclongnextLong(){returnLong.parseLong(next());}
							}
							
							publicclassWatchingMooloo{
							publicstaticvoidmain(String[]args){
							
							Kattioio=newKattio();
							intN=io.nextInt();
							intK=io.nextInt();
							ArrayList<Long>days=newArrayList<>();
							longans=0,yesterday=0;
							for(inti=0;i<N;i++){
							longtoday=io.nextLong();
							if(i==0){
							ans+=K+1;
							}
							else{
							longnoOfDays=today-yesterday;
							ans+=Math.min(noOfDays,K+1);
							}
							yesterday=today;
							}
							io.print(ans);
							io.close();
							}
							}


```
</details>

### Day 7, 8 & 9: April 8, 9 & 10, 2024

**Progress**:
Something personal came up, which caused an erratic schedule, which resulted in, not being able to provide daily updates. However, I have been progressing daily, albait, the rate of progress did get a hit, but not by much. Revised a bunch of problems, that had been in backlog for a long long time as well as gave leetcode contest (which I am targeting to upsolve, by the end of this week.)

**Thoughts**:
Below, are the list of the problems, that I have revised (its a lot):

<details>
	<summary><a href="https://codeforces.com/contest/1556/problem/D">Take a guess</a></summary>
Its a math trickery problem. Basically, we have to find the kth smallest no, in the series, however, the series is not given. The only thing, we can do is, ask the program for either providing the & of nos. at i and j, else get the "or" value of nos. i and j. We will, however, be given the value of k. So if we somehow find (a+b), (b+c) and (a+c), we will be able to find a. However, since we will only get & and "or", what should we do ? Turns out, you if you are familiar with the below equations, the problems, takes seconds to solve. The math expressions are :<br>
i) a+b = 2*(a&b)+(a xor  b) <br>
ii) a xor b = not(a and b) and (a or b) <br>
Well, the proof of the first equation, was really interesting!
<br>
Implementation:

 ```java
import java.io.BufferedReader;
import java.io.IOException;
import java.io.InputStreamReader;
import java.util.ArrayList;
import java.util.Collections;
import java.util.Comparator;
import java.util.StringTokenizer;

public class TakeAGuess {
    static BufferedReader x=new BufferedReader(new InputStreamReader(System.in));
    public static void main(String[] args)throws IOException {
        StringTokenizer st=new StringTokenizer(x.readLine());
        int n=Integer.parseInt(st.nextToken());
        int k=Integer.parseInt(st.nextToken());
        ArrayList<Integer> arr=new ArrayList<>();
        fillInFirstThree(arr);
        for(int i=4;i<=n;i++){
            arr.add(getSum(i-1,i)-arr.get(arr.size()-1));
        }
        Collections.sort(arr);
        System.out.println("finish "+arr.get(k-1));
    }
    static void fillInFirstThree(ArrayList<Integer> arr)throws IOException{
        int aPLUSb=getSum(1,2);
        int bPLUSc=getSum(2,3);
        int aPLUSc=getSum(1,3);
        int a=((aPLUSb+aPLUSc)-(bPLUSc))/2;
        int b=aPLUSb-a;
        int c=bPLUSc-b;
        arr.add(a);
        arr.add(b);
        arr.add(c);
    }
    static int getSum(int a, int b)throws IOException{
        int aANDb=askForAnd(a,b);
        int aORb=askForOr(a,b);
        int aXORb=aORb&(~aANDb);
        return (2*aANDb)+aXORb;
    }
    static int askForAnd(int a, int b)throws IOException{
        String space=" ";
        System.out.println("and"+space+a+space+b);
        return Integer.parseInt(x.readLine());
    }
    static int askForOr(int a, int b)throws IOException{
        String space=" ";
        System.out.println("or"+space+a+space+b);
        return Integer.parseInt(x.readLine());
    }
}
 
```
</details>
<details>
	<summary><a href="https://leetcode.com/problems/maximum-number-of-groups-with-increasing-length/description/">Max. No. of Groups with increasing length</a></summary>
Leetcode problem. It’s a tricky one, if you dry run and draw stuff, you will guess the pattern. But its one of those unique questions, where you understand the problem, as well as the solution, meaning, figuring out, what's the pattern, however, it still, would be difficult to implement. Its was for me. I was stuck, with the thought, that for creating groups, the frequency should be somehow "atleast" and played a lot around it, however, then I remembered, we can use sum, in order to compensate for the usageLimits larger than the atleast usageLimit. Thereby, arriving towards the solution. I experimented a lot.
<br>Implementation:

```java
class Solution {
    public int maxIncreasingGroups(List<Integer> usageLimits) {
        // return answer1(usageLimits);
        // return answer2(usageLimits);
        // return answer3(usageLimits);
        // return answer4(usageLimits);
        return answer5(usageLimits);
    }
    // int answer5(List<Integer> usageLimits){
    //     Collections.sort(usageLimits);
    //     List<Long> usageLimitsSum=new ArrayList<Long>();
    //     usageLimitsSum.add(usageLimits.get(0));
    //     for(int i=1;i<usageLimits.size();i++){
    //         usageLimitsSum.add(usageLimitsSum.get(i-1)+usageLimits.get(i));
    //     }
    //     int group=0;
    //     for(int i=1;i<=usageLimits.size();i++){
    //         if(!binarySearch(i, usageLimitsSum))
    //             ++group;
    //     }
    //     return group;
    // }
    // boolean binarySearch(int key, List<Long> arr){
    //     // long key=
    //     if(arr.get(arr.size()-1)<m)return false;
    //     int l=0, r=arr.size()-1, m=0;
    //     while(l<r){
    //         m=(l+r)/2;
    //         if(arr.get(l)<key){
    //             l=m;
    //         }
    //         else if(arr.get(r)>key){
    //             r=m;
    //         }
    //         else{
    //             return true;
    //         }
    //     }
    // }
    int answer4(List<Integer> usageLimits){
        Collections.sort(usageLimits);
        long m=1; long sum=0;
        for(int i:usageLimits){
            sum+=i;
            // System.out.println(sum+" "+m+" "+m*(m+1)/2);
            if(m*(m+1)/2 <= sum){
                ++m;
            }
            // else{
            //     return m-1;
            // }
        }
        return (int) m-1;
    }
    int answer3(List<Integer> usageLimits){
        Collections.sort(usageLimits);
        int answer=0, total=0,m=1;
        for(int i:usageLimits){
            total+=i;
            if(total>=(m*(m+1))/2){
                ++answer;
            }
            ++m;
        }
        return answer;
    }
    int answer2(List<Integer> usageLimits){
        int arr[]=new int[usageLimits.size()];
        for(int i=0;i<arr.length;i++){
            arr[i]=usageLimits.get(i);
        }
        Arrays.sort(arr);
        int answer=0;
        for(int j=arr.length-1;j>=0;j--){
            for(int i=arr.length-1;i>=j;i--){
                --arr[i];
                if(arr[i]<0)return answer;
            }
            ++answer;
        }
        return answer;
    }
    int answer1(List<Integer> usageLimits){
        PriorityQueue<Integer> pq=new PriorityQueue<Integer>((a,b)->b-a);
        for(int i:usageLimits){
            pq.add(i);
        }
        int answer=0;
        for(int i=1;i<=usageLimits.size();i++){
            List<Integer> arr=new ArrayList<>();
            for(int j=0;j<i;j++){
                arr.add(pq.poll());
            }
            for(int j:arr){
                if(j==0)return answer;
                pq.add(j-1);
            }
            ++answer;
        }
        return answer;
    }
}

```
</details>
<details>
	<summary><a href="https://codeforces.com/contest/1338/problem/A">Powered Addition</a></summary>
a tricky question, indeed! But, with hit and trial, it can be figured out, or atleast get some ideas. Like, firstly, there is no need of increasing the value of any array element, more than the max value. Second, the only difference, that's important is, the max difference between the ith and jth number where i&lt;j. Finding the power of 2, of that difference, solves the problem. Finding the power of 2, of that difference, solves the problem.
<br>Implementation:

```java

public class PoweredAddition {
    static FastReader x=new FastReader();
    static int testCases;

    public static void main(String[] args)throws IOException {
//        x=new FastReader("D:\\usaco.guide\\testFile.txt");
        testCases=x.nextInt();
        getAnswer1();
    }
    static void getAnswer1(){
        int n = 0;
        long arr[];
        class method{
            void perProcess(long arr[], int n) {
                long min=0;
                for(int i=0;i<n;i++){
                    min=Math.min(arr[i],min);
                }
                min=min*-1;
                for(int i=0;i<n;i++){
                    arr[i]+=min;
                }
            }
            int getPowerOf(long c){
                int pow=0;
                for(int i=1;i<33;i++){
                    if((c&1)==1){
                        pow=i;
                    }
                    c=c>>1;
                }
                return pow;
            }
        }
        method m=new method();
        for(int i=0;i<testCases;i++){
            n= x.nextInt();
            arr= new long[n];
            for(int j=0;j<n;j++){
                arr[j]=x.nextInt();
            }
            m.perProcess(arr, n);
//            System.out.println(Arrays.toString(arr));
            long diff=0;
            for(int j=1;j<n;j++){
                diff=Math.max(diff, arr[j-1]-arr[j]);
                arr[j]=Math.max(arr[j], arr[j-1]);
            }
            System.out.println(m.getPowerOf(diff));
        }
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
```
</details>

<details>
	<summary><a href="https://atcoder.jp/contests/abc295/tasks/abc295_d">Three Days</a></summary>
The question is very easy to understand, however, the solution, either requires considerable practice, math brain, as there is a special trick involved to observe. This really fried my brain. So, for each digit of that no., we xor its place, and store it, if there is a place, where the string is palindrome, then, its place automatically incremented. Yeah, it’s a bit confusing, its best to view the editorial. I had experimented with various approaches, before arriving to the solution, all listed below.
<br>Implementation:

 ```java
import java.io.BufferedReader;
import java.io.IOException;
import java.io.InputStreamReader;
import java.util.*;

public class Main {
    static BufferedReader x=new BufferedReader(new InputStreamReader(System.in));
    static String s;
    static HashSet<Pair> hs=new HashSet<Pair>();
    public static void main(String[] args)throws IOException {
        s=x.readLine();
//        getAnswer1();
//        getAnswer2();
//        getAnswer3();
        getAnswer4();
//        System.out.println("Unmatched: "+hs);
    }
    static void getAnswer4(){
        char sArr[]=s.toCharArray();
        HashMap<ArrayList<Integer>, Integer> hm=new HashMap<ArrayList<Integer>, Integer>();
        ArrayList<Integer> arr=new ArrayList<>();
        for(int i=0;i<10;i++){
            arr.add(0);
        }
        hm.put(arr,1);
        long res=0;
        for(char c:sArr){
            arr.set(c-'0', (arr.get(c-'0')+1)%2);
            long temp=hm.getOrDefault(arr,0);
            res+=temp;
            hm.put(arr, hm.getOrDefault(arr, 0)+1);
        }
        System.out.println(res);
    }
    static void getAnswer3()throws IOException{
        char sArr[]=s.toCharArray();
        long arr[]=new long[1<<10];
        int temp=0;
        ++arr[0];
        for(char c:sArr){
            temp=temp^(1<<(c-'0'));
//            System.out.println(temp);
            ++arr[temp];
        }
        long res=0;
        for(int i=0;i<1<<10;i++){
            res=res+((arr[i]*(arr[i]-1))/2);
        }
        System.out.println(res);
    }
    static void getAnswer1()throws IOException{
        char sArr[]=s.toCharArray();
        int ans=0;
        for(int i=0;i<s.length();i++){
            int temp=0;
            for(int j=i;j<s.length();j++){
                int t=sArr[j]-'0';
                temp^=(1<<t);
                if(temp==0){
                    hs.add(new Pair(i,j));
                    ++ans;
                }
            }
        }
        System.out.println(ans);
    }
    static void getAnswer2()throws IOException{
        char sArr[]=s.toCharArray();
        int ans=0;
        for(int i=0;i<s.length();i++){
            int temp=0;
            for(int j=i;j<s.length();j++){
//                int t=sArr[j]-'0';
                temp=temp^sArr[j];
//                System.out.println(i+", "+j+": "+temp);
                if(temp==0){
                    findOut(i,j);
//                    System.out.println(i+" "+j);
                    ++ans;
                }
            }
        }
        System.out.println(ans);
    }
    static void findOut(int a, int b){
        Pair p=new Pair(a,b);
        if(hs.contains(p)){
            hs.remove(p);
        }
        else{
            System.out.print("Extra: "+a+","+b+" || ");
            int xorNo=0, xorChar=0;
            for(int i=a;i<=b;i++){
                System.out.print(s.charAt(i));
                xorNo^=(s.charAt(i)-'0');
                xorChar^=s.charAt(i);
            }
            System.out.println("  || "+xorNo+" "+xorChar);
        }
    }
}
class Pair{
    int a, b;
    Pair(int x, int y){
        a=x;
        b=y;
    }
    Pair(char x, char y){
        a=x-'0';
        b=y-'0';
    }
    @Override
    public int hashCode() {
        final int prime = 31;
        int result = 1;
        result = prime * result + a;
        result = prime * result + b;
        return result;
    }
    @Override
    public String toString(){
        return "("+a+","+b+")";
    }
    @Override
    public boolean equals(Object obj) {
        Pair p=(Pair)obj;
        if (a != p.a)
            return false;
        if (b != p.b)
            return false;
        return true;
    }
}
```
</details>
<details>
	<summary><a href="https://codeforces.com/contest/1017/problem/D">The Wu</a></summary>
	The problem is, a bit tricky to understand, however, once you understand the question, its pretty simple to solve. See, since the constraint of k is 100, for any input, you can pretty much, presolve answers and store it in array and then, display those stuff, wrt query, and query size suddenly doesn't have any effect. Else, for each query, there will be computation time wasted. 
<br>Below are the implementations, I experimented with:

```java
public class TheWu {
    static BufferedReader x=new BufferedReader(new InputStreamReader(System.in));
    static PrintWriter pw=new PrintWriter(System.out);
    public static void main(String[] args)throws IOException {
        String file="D:\\usaco.guide\\testFile.txt";
//        x=new BufferedReader(new FileReader(file));

        StringTokenizer st=new StringTokenizer(x.readLine());
        int n=Integer.parseInt(st.nextToken());
        int m=Integer.parseInt(st.nextToken());
        int q=Integer.parseInt(st.nextToken());
        int w[]=new int[n];
        st=new StringTokenizer(x.readLine());
        for(int i=0;i<n;i++){
            w[i]=Integer.parseInt(st.nextToken());
        }
//        int multiSet_S[]=new int[m];
        HashMap<Integer, Integer> hm=new HashMap<>();
        HashSet<Integer> multiSet_S=new HashSet<>();
        for(int i=0;i<m;i++){
            int temp=stringToInteger(x.readLine());
            multiSet_S.add(temp);
            hm.put(temp, hm.getOrDefault(temp,0)+1);
        }
        int combinations=(int)Math.pow(2,n);
        int k_wu[][]=new int[101][combinations+1];
//        HashMap<Integer, ArrayList<Integer>> hm=new HashMap<>();
        for(int c=0;c<combinations;c++){
//            if(hm.containsKey(c))continue;
//            hm.put(c, new ArrayList<>());
            for(int s:multiSet_S){
                int wu=wuOf(c,s,w);
//                System.out.println(wu);
                if(wu<=100){
                    k_wu[wu][c]+=hm.get(s);
                }
//                hm.get(c).add(wu);
            }
//            Collections.sort(hm.get(c));
        }

        for(int j=0; j<combinations; j++){
            for(int i=1;i<=100;i++){
                k_wu[i][j]+=k_wu[i-1][j];
            }
        }

//        for(int i=0;i<=100;i+=10){
//            System.out.println(Arrays.toString(k_wu[i]));
//        }
        ArrayList<Integer> answers=new ArrayList<>();
        for(int i=0;i<q;i++){
            st=new StringTokenizer(x.readLine());
            int t=stringToInteger(st.nextToken());
            int k=Integer.parseInt(st.nextToken());
//            int tempAns=binarySearch(hm.get(t),k);
            answers.add(k_wu[k][t]);
        }
        for(int i:answers){
            pw.println(i);
        }
        pw.close();
    }
    static int binarySearch(ArrayList<Integer> arr, int k){
        int l=0, r=arr.size()-1;
        while(l<=r){
            int m=(l+r)/2;
            if(arr.get(m)>k){
                r=m-1;
            }
            else if(arr.get(m)<k){
                l=m+1;
            }
            else{
                l=m+1;
            }
        }
        return l;
    }
    static int stringToInteger(String s){
        int ans=0, prd=1;
        for(int i=s.length()-1;i>=0;i--){
            if(s.charAt(i)=='1'){
                ans+=prd;
            }
            prd*=2;
        }
        return ans;
    }
    static int wuOf(String s, String t, int w[]){
        int ans=0;
        for(int i=0;i<s.length();i++){
            if(s.charAt(i)==t.charAt(i)){
                ans+=w[i];
            }
        }
        return ans;
    }
    static int wuOf(int s, int t, int w[]){
        int ans=0;
        for(int i=w.length-1;i>=0;i--){
            if((s&1)==(t&1)){
                ans+=w[i];
            }
            s=s>>1;
            t=t>>1;
        }
        return ans;
    }
} 
```
</details>
<details>
	<summary><a href="https://codeforces.com/contest/1851/problem/F">Lisa and the Martians</a></summary>
This question too, is, kinda simple to understand, however, to solve it, need to be familiar with these kinds of probs. If you ever feel uncontrobly and narcestically good about yourself, I strongly recommend, solving algorithmic problems, it will keep you in place…. Lol! Well, so the train of thought here is, pay attention to the equation, there is a big ass "&" on the equation, so, we can kinda assume, that, to maximize the solution, we need max. no. of 1's. So we first sort the array, and find the lowest xor value, between two nos. This is because, if you recall the xor truth table, then, its 0 for values. Once we get this value, then we find the k, to max out the value, by either multiplying by 2 until k or right shifting until k. Below implementation, contains, almost all the trial errors, I have made :

  ```java
public class LisaAndMartians {
    static BufferedReader x=new BufferedReader(new InputStreamReader(System.in));
    static PrintWriter pw=new PrintWriter(System.out);
    static int arr[];
    static int n;
    static int k;
    public static void main(String[] args) throws IOException {
        int testCases=Integer.parseInt(x.readLine());
        for(int i=0;i<testCases;i++){
            StringTokenizer st=new StringTokenizer(x.readLine());
            n=Integer.parseInt(st.nextToken());
            k=Integer.parseInt(st.nextToken());
            st=new StringTokenizer(x.readLine());
            arr=new int[n];
            for(int j=0;j<n;j++){
                arr[j]=Integer.parseInt(st.nextToken());
            }
//            getAnswer1();
//            getAnswer3();
//            getAnswer2();
//            getAnswer5();
//            getAnswer4();
//            getAnswer6();
            getAnswer7();
        }
        pw.close();
    }
    static void getAnswer7(){
        int ar[][]=new int[arr.length][2];
        for(int i=0;i<arr.length;i++){
            ar[i][0]=arr[i];
            ar[i][1]=i;
        }
        Arrays.sort(ar,(int a[], int b[])->a[0]-b[0]);
        int lowest=Integer.MAX_VALUE, lowestI=0;
        for(int i=1;i<arr.length;i++){
            int temp=ar[i-1][0]^ar[i][0];
            if(temp<lowest){
                lowest=temp;
                lowestI=i;
            }
        }
        System.out.println((ar[lowestI-1][1]+1)+" "+(ar[lowestI][1]+1)+" "+(((1<<k)-1)^ar[lowestI][0]));
    }
    static void getAnswer6(){
        int and=0, ai=0, aj=0;
        for(int i=0;i<n;i++){
            for(int j=i+1;j<n;j++){
                int temp=arr[i]&arr[j];
                if(and<=temp){
                    and=temp;
                    ai=i;
                    aj=j;
                }
            }
        }
        and=0;int x=0;
        for(int i=0;i<1<<k;i++){
            int temp=((arr[ai]^i)&(arr[aj]^i));
            if(and<temp){
                and=temp;
                x=i;
            }
        }
        System.out.println(ai+" "+aj+" "+x);
//        System.out.print(ai+" "+aj+" "+x+" ---- "+((arr[ai]^x)&(arr[aj]^x))+" ");
        getAnswer2();
    }
    static void getAnswer4(){
        HashMap<Integer, Queue<Integer>> hm=new HashMap<>();
        for(int i=0;i<n;i++){
            if(!hm.containsKey(arr[i])){
                hm.put(arr[i], new LinkedList<>());
            }
            hm.get(arr[i]).add(i);
        }
        Arrays.sort(arr);
//        System.out.println(hm);
        int ai=0, aj=0, x=0;
        int ans=0;
        for(int i=1;i<n;i++){
            for(int j=0;j<1<<k;j++){
                int temp=(arr[i-1]^j)&(arr[i]^j);
                if(ans<=temp){
                    ai=hm.get(arr[i-1]).poll();
                    aj=hm.get(arr[i]).poll();
                    x=j;
                    hm.get(arr[i-1]).add(ai);
                    hm.get(arr[i]).add(aj);
                    ans=temp;
                }
            }
        }
//        System.out.println((ai+1)+" "+(aj+1)+" "+x);
        pw.println((ai+1)+" "+(aj+1)+" "+x);
    }
    static void getAnswer1(){
        int highestAnd=0, ai=0, aj=0;
        for(int i=0;i<n;i++){
            for(int j=i+1;j<n;j++){
                if(highestAnd<(arr[i]&arr[j])){
                    highestAnd=arr[i]&arr[j];
                    ai=i;
                    aj=j;
                }
            }
        }
        int ans=0, x=0;
        for(int i=0;i<1<<k;i++){
            if(ans<(highestAnd^(arr[ai]&i)^(arr[aj]&i))){
                ans=highestAnd^(arr[ai]&i)^(arr[aj]&i);
                x=i;
            }
        }
        System.out.println((ai+1)+" "+(aj+1)+" "+x+" -- "+ans);
    }
    static void getAnswer2(){
        int highest=0, ai=0, aj=0, x=0;
        for(int i=0;i<n;i++){
            for(int j=i+1;j<n;j++){
                for(int xc=0;xc<1<<k;xc++){
                    int temp=((arr[i]^xc)&(arr[j]^xc));
                    if(highest<=temp){
                        ai=i;
                        aj=j;
                        x=xc;
                        highest=temp;
                    }
                }
            }
        }
//        System.out.println(highest+","+x);
//        System.out.println((ai+1)+" "+(aj+1)+" "+x);
        System.out.println((ai+1)+" "+(aj+1)+" "+x);
//        System.out.println(ai+" "+aj+" "+x+" -- "+highest);
    }
    static void getAnswer5(){
        int highest=0, ai=0, aj=0, x=0;
        for(int i=0;i<n;i++){
            for(int j=i+1;j<n;j++){
                int xc=1<<k;
                for(int l=0;l<100;l++){
                    if(xc<0)break;
                    --xc;
                    int temp=((arr[i]^xc)&(arr[j]^xc));
                    if(highest<=temp){
                        ai=i;
                        aj=j;
                        x=xc;
                        highest=temp;
                    }
                }
            }
        }
        System.out.println((ai+1)+" "+(aj+1)+" "+x);
//        System.out.print(k+" | "+(1<<k)+" || "+(ai+1)+" "+(aj+1)+" "+x+" -- "+highest+" ");
//        getAnswer2();
//        System.out.println(ai+" "+aj+" "+x+" -- "+highest);
    }
    static void getAnswer3(){
        int highestA=0, x=0, ai=0;
        for(int xc=0;xc<1<<k;xc++){
            for(int i=0;i<n;i++){
                int temp=arr[i]^xc;
                if(highestA<temp){
                    highestA=temp;
                    x=xc;
                    ai=i;
                }
            }
        }
        int bi=0, highestB=0;
        for(int i=0;i<n;i++){
            if(i==ai)continue;
            int temp=(highestA)&(arr[i]^x);
            if(highestB<temp){
                highestB=temp;
                bi=i;
            }
        }
        System.out.println(ai+" "+bi+" "+x+" -- "+(highestA&highestB));
    }
}
 ```
</details>
<details>
	<summary><a href="https://leetcode.com/problems/maximize-the-profit-as-the-salesman/description/">Maximize the Profit as the Salesman</a></summary>
The question is simple to understand and if familiar with recursion and dp, then its an easy solve. Basically, sort according the the start index, and for every node, recursively find, the max. sale price value and find them max value. However, what if there are multiple nodes in the start index ? You can either loop and traverse the equal start index coordinates, or use binary search to further optimize it by finding the end point of that start index and where the next start index occures. First, you can apply a recursive equation to solve the problem, it will give tle, however, no worries, memoise the solution and you are good to go. 
<br>Below are my implementation experiements.

 ```java
class Solution {
    public int maximizeTheProfit(int n, List<List<Integer>> offers) {
        // return answer2(n, offers);
        /*memisation*/
        // return answer3(n, offers);
        /*simple bruteForce recursion*/
        // return answer4(n, offers);
        /*Optimised*/
        // return answer5(n, offers);
        /*optimised binary search*/
        return answer6(n, offers);
    }
    int answer6(int n, List<List<Integer>> offers){
        Collections.sort(offers,(a,b)->a.get(0)-b.get(0));
        // System.out.println(offers);
        return function4(offers.get(0).get(0), offers);
    }
    int function4(int currentIndex, List<List<Integer>> offers){
        if(currentIndex>=offers.size()){
            return 0;
        }
        int max=0, curr=currentIndex;
        int nextIndex=getNextIndex(currentIndex, offers);
        boolean flag=true;
        while(flag){
            max=Math.max(max, offers.get(curr).get(2)+function4(nextIndex, offers));
            ++curr;
            if(curr>=offers.size()||offers.get(currentIndex).get(0)!=offers.get(curr).get(0)){
                flag=false;
            }
        }
        return max;
    }
    int getNextIndex(int currentIndex, List<List<Integer>> offers){
        int l=currentIndex, r=offers.size();
        while(l<r){
            int m=(l+r)/2;
            if(offers.get(m).get(0)>offers.get(currentIndex).get(1)){
                ++l;
            }
            else{
                --r;
            }
        }
        return r;
    }
    int dp1[];
    int answer5(int n, List<List<Integer>> offers){
        // Collections.sort(offers,(a,b)->a.get(0)-b.get(0));
        // System.out.println(offers);
        HashMap<Integer, List<List<Integer>>> hm=new HashMap<Integer, List<List<Integer>>>();
        for(int i=0;i<n;i++){
            hm.put(i, new ArrayList<List<Integer>>());
        }
        dp1=new int[n];
        int min=1000000;
        for(List<Integer> ls:offers){
            min=Math.min(min, ls.get(0));
            hm.get(ls.get(0)).add(ls);
        }
        // System.out.println(hm);
        return function3(min, hm);
    }
    int function3(int currentIndex, HashMap<Integer, List<List<Integer>>> hm){
        if(currentIndex>=hm.size()){
            return 0;
        }
        if(dp1[currentIndex]>0)return dp1[currentIndex];
        int max=0;
        for(List<Integer> ls:hm.get(currentIndex)){
            max=Math.max(max, ls.get(2)+function3(ls.get(1)+1, hm));
        }
        max=Math.max(max, function3(currentIndex+1, hm));
        dp1[currentIndex]=max;
        return max;
    }
    int answer2(int n, List<List<Integer>> offers){
        Collections.sort(offers,(a,b)->a.get(0)-b.get(0));
        // System.out.println(offers);
        return function2(0, offers, -1);
    }
    int answer4(int n, List<List<Integer>> offers){
        Collections.sort(offers,(a,b)->a.get(0)-b.get(0));
        // System.out.println(offers);
        return function1(0, offers, -1);
    }
    int dp[][];
    public int answer3(int n, List<List<Integer>> offers){
        Collections.sort(offers,(a,b)->a.get(0)-b.get(0));
        // System.out.println(offers);
        dp=new int[offers.size()][offers.size()+1];
        return function(0, offers, -1);
    }
    int function(int currentIndex, List<List<Integer>> offers, int prevIndex){
        if(currentIndex>=offers.size()){
            return 0;
        }
        // System.out.println(currentIndex+" "+prevIndex);
        if(dp[currentIndex][prevIndex+1]!=0)return dp[currentIndex][prevIndex+1];
        if(overlap(offers,currentIndex, prevIndex)){
            dp[currentIndex][prevIndex+1]= function(currentIndex+1, offers, prevIndex);
            return dp[currentIndex][prevIndex+1];
        }
        else{
        dp[currentIndex][prevIndex+1]= Math.max(offers.get(currentIndex).get(2)+function(currentIndex+1, offers, currentIndex),function(currentIndex+1, offers, prevIndex));
        return dp[currentIndex][prevIndex+1];
        }
    }
    int function1(int currentIndex, List<List<Integer>> offers, int prevIndex){
        if(currentIndex>=offers.size()){
            return 0;
        }
        // System.out.println(currentIndex+" "+prevIndex);
        if(overlap(offers,currentIndex, prevIndex)){
            return function1(currentIndex+1, offers, prevIndex);
        }
        else{
            return Math.max(offers.get(currentIndex).get(2)+function1(currentIndex+1, offers, currentIndex),function1(currentIndex+1, offers, prevIndex));
        }
    }
    int function2(int currentIndex, List<List<Integer>> offers, int prevIndex){
        if(currentIndex>=offers.size()){
            return 0;
        }
        // System.out.println(currentIndex+" "+prevIndex);
        if(overlap(offers,currentIndex, prevIndex)){
            // return function2(currentIndex+1, offers, prevIndex);
            return Math.max(function2(currentIndex+1, offers, currentIndex),function2(currentIndex+1, offers, prevIndex));
        }
        else{
            return offers.get(currentIndex).get(2)+function2(currentIndex+1, offers, currentIndex);
        }
    }
    boolean overlap(List<List<Integer>> offers, int currentIndex, int prevIndex){
        if(prevIndex==-1)return false;
        int bStart=offers.get(currentIndex).get(0);
        int bEnd=offers.get(currentIndex).get(1);
        int aStart=offers.get(prevIndex).get(0);
        int aEnd=offers.get(prevIndex).get(1);
        // System.out.println("==>"+bStart+" "+aEnd);
        if(bStart<=aEnd)return true;
        return false;
    }
}
						
```
</details>
<details>
	<summary><a href="https://leetcode.com/problems/find-the-minimum-possible-sum-of-a-beautiful-array/description/">Find the Minimum Possible Sum of a Beautiful Array</a></summary>
A simple problem, just some observation, simple to understand and simple to solve really. Just start coding whatever you think, either you will end up solving it, or you will be very very close!

 ```java
class Solution {
    public long minimumPossibleSum(int n, int target) {
        int k=0;
        HashSet<Integer> hs=new HashSet<Integer>();
        long sum=0;
        for(int i=1; i<=n;){
            ++k;
            if(hs.contains(target-k))continue;
            ++i;
            hs.add(k);
            sum+=k;
        }
        return sum;
    }
}
```
</details>
<details>
	<summary><a href="https://leetcode.com/problems/determine-the-minimum-sum-of-a-k-avoiding-array/description/">Determine the Minimum Sum of a k-avoiding Array</a></summary>
Just a small variation to the above problem. 

```java
class Solution {
    public int minimumSum(int n, int k) {
        HashSet<Integer> hs=new HashSet<Integer>();
        int i=1;
        while(hs.size()<n){
            if(!hs.contains(k-i)){
                hs.add(i);
            }
            ++i;
        }
        int ans=0;
        for(int ii:hs){
            ans+=ii;
        }
        System.out.println(hs);
        return ans;
    }
}
```
</details>
<details>
<summary><a href="https://leetcode.com/problems/check-if-strings-can-be-made-equal-with-operations-i/">Check if Strings Can be Made Equal With Operations I</a></summary>
					Easy to understand problem, no such extra need to know algorithm and datastructure involved, just plain problem solving. The trick is, how you view the problem. The one thing, that really helped me was, first doing brute force of this, because, its very easy to do a brute force and it even works. Then after spending some time, thinking to optimize it, you will figure out how to use maps. As there are no logn algos or datastructures, that can help!! Lol.

````java
class Solution {
    public boolean canBeEqual(String s1, String s2) {
        HashMap<Character, HashSet<Integer>> hm=new HashMap<Character, HashSet<Integer>>();
        for(int i=0;i<s1.length();i++){
            if( !hm.containsKey(s1.charAt(i)) ){
                hm.put(s1.charAt(i), new HashSet<Integer>());   
            }
            hm.get(s1.charAt(i)).add(i);
        }
        for(int i=0;i<s2.length();i++){
            // System.out.println(s2.charAt(i)+" "+hm);
            if(!hm.containsKey(s2.charAt(i)))return false;
            // System.out.println("--");
            if(hm.get(s2.charAt(i)).contains(i))hm.get(s2.charAt(i)).remove(i);
            // else if(hm.get(s2.charAt(i)).contains(i+1))hm.get(s2.charAt(i)).remove(i+1);
            else if(hm.get(s2.charAt(i)).contains(i+2))hm.get(s2.charAt(i)).remove(i+2);
            // else if(hm.get(s2.charAt(i)).contains(i-1))hm.get(s2.charAt(i)).remove(i-1);
            else if(hm.get(s2.charAt(i)).contains(i-2))hm.get(s2.charAt(i)).remove(i-2);
            else return false;
        }
        return true;
    }
}
````
</details>
<details>
<summary><a href="https://leetcode.com/problems/count-symmetric-integers/">Count Symmetric Integers</a></summary>
					Well, I am not proud of this solution. Didn't want to give more time here…. As it was during contest and I knew it would pass tc. As you can see, there is a lot of wiggle room, to save tc.

````java
class Solution {
    public int countSymmetricIntegers(int low, int high) {
        int ans=0;
        for(int i=low;i<=high;i++){
            if((i+"").length()%2!=0)continue;
            if(check(i))++ans;
        }
        return ans;
    }
    boolean check(int num){
        String str=num+"";
        int sum1=0, sum2=0;
        for(int i=0;i<str.length()/2;i++){
            sum1+=Integer.parseInt(str.charAt(i)+"");
        }
        for(int i=str.length()/2;i<str.length();i++){
            sum2+=Integer.parseInt(str.charAt(i)+"");
        }
        return sum1==sum2;
    }
}
````
</details>
<details>
<summary><a href="https://leetcode.com/problems/find-the-longest-equal-subarray/">Find the Longest Equal Subarray</a></summary>
					Simple to understand, however, tricky to deliver. Tried multiplie attempts, listed all below. The solution was mesmarising, though, which involves the sliding window concept. Where we slide through the array, and for the element in that index, we delete those elements and see the size of the array left, if its greater than k, the second pointer moves forward, until it normalizes. I understad, the explanation might not be helpful, however, I suggest, for better understanding, see the answer4() below, and try drawing it. If you are able to draw the simulation successfully, you will easily understand the problem

````java
class Solution {
    int specialCase(List<Integer> nums){
        int prevValue=nums.get(0);
        int ans=0, temp=1;
        for(int i=1;i<nums.size();i++){
            if(prevValue==nums.get(i)){
                ++temp;
            }
            else{
                ans=Math.max(temp, ans);
                temp=1;
                prevValue=nums.get(i);
            }
        }
        ans=Math.max(temp,ans);
        return ans;
    }
    int answer1(List<Integer> nums, int k){
        if(k==0)return specialCase(nums);
        HashMap<Integer, Deque<Integer>> hm=new HashMap<Integer, Deque<Integer>>();
        HashMap<Integer, Deque<Integer>> hmReverse=new HashMap<Integer, Deque<Integer>>();
        for(int i=0;i<nums.size();i++){
            if(hm.containsKey(nums.get(i)))continue;
            int noOfObstructions=0;
            Deque<Integer> pq=new ArrayDeque<Integer>();
            for(int j=i;j<nums.size();j++){
                if(nums.get(j)==nums.get(i)){
                    pq.add(noOfObstructions);
                    noOfObstructions=0;
                }
                else{
                    ++noOfObstructions;
                }
            }
            hm.put(nums.get(i), new ArrayDeque<Integer>(pq));
        }
        for(int i=nums.size()-1;i>=0;i--){
            if(hmReverse.containsKey(nums.get(i)))continue;
            int noOfObstructions=0;
            Deque<Integer> pq=new ArrayDeque<Integer>();
            for(int j=i;j>=0;j--){
                if(nums.get(j)==nums.get(i)){
                    pq.add(noOfObstructions);
                    noOfObstructions=0;
                }
                else{
                    ++noOfObstructions;
                }
            }
            hmReverse.put(nums.get(i), new ArrayDeque<Integer>(pq));
        }
        int ans=0;
        System.out.println(hm);
        System.out.println(hmReverse);
        for (Map.Entry<Integer, Deque<Integer>> entry : hm.entrySet()) {
            int k1=0, a=0;
            Deque<Integer> pq=entry.getValue();
            while(!pq.isEmpty()){
                int temp=pq.removeFirst();
                // System.out.println(entry.getKey()+" : "+temp);
                k1+=temp;
                if(k1<=k){
                    ++a;
                }
                else{
                    break;
                }
            }
            ans=Math.max(ans, a);
        }
        for (Map.Entry<Integer, Deque<Integer>> entry : hmReverse.entrySet()) {
            int k1=0, a=0;
            Deque<Integer> pq=entry.getValue();
            while(!pq.isEmpty()){
                int temp=pq.removeFirst();
                // System.out.println(entry.getKey()+" : "+temp);
                k1+=temp;
                if(k1<=k){
                    ++a;
                }
                else{
                    break;
                }
            }
            ans=Math.max(ans, a);
        }
        return ans;
    }
    public int longestEqualSubarray(List<Integer> nums, int k) {
        // return answer1(nums, k);
        // return answer2(nums, k);
        // return answer3(nums, k);
        // return answer4(nums, k);
        // return answer5(nums, k);
        return answer6(nums.stream().mapToInt(i -> i).toArray(), k);
    }
    int answer6(int v[], int k ){
        int n = v.length;
        int i = 0;
        int j = 0;
        Map<Integer, Integer> m = new HashMap<>();
        int ans = 1;
        int mf = 0;
        while (j < n) {
            m.put(v[j], m.getOrDefault(v[j], 0) + 1);
            mf = Math.max(mf, m.get(v[j]));
            int rem = j - i + 1 - mf;
            if (i < n && rem > k) {
                m.put(v[i], m.get(v[i]) - 1);
                i++;
                // mf = Math.max(mf, m.get(v[j]));
                rem = j - i + 1 - mf;
            }
            // ans = Math.max(ans, mf);
            j++;
        }
        return mf;
    }
     int answer5(List<Integer> A, int k) {
        int maxf = 0, i = 0, n = A.size();
        Map<Integer, Integer> count = new HashMap<>();
        for (int j = 0; j < n; j++) {
            count.put(A.get(j), count.getOrDefault(A.get(j), 0) + 1);
            maxf = Math.max(maxf, count.get(A.get(j)));
            if (j - i + 1 - maxf > k) {
                count.put(A.get(i), count.get(A.get(i)) - 1);
                i++;
            }
        }
        return maxf;
    }
    int answer4(List<Integer> nums, int k){
        HashMap<Integer, Integer> hm=new HashMap<Integer, Integer>();
        int j=0, count=0;
        for(int i=0;i<nums.size();i++){
            int noInI=nums.get(i);
            int noInJ=nums.get(j);
            hm.put(noInI, hm.getOrDefault(noInI,0)+1);
            count=Math.max(count, hm.get(noInI));
            if(i-j+1-count>k){
                hm.put(noInJ, hm.get(noInJ)-1);
                ++j;
            }
        }
        return count;
    }
    int answer3(List<Integer> nums, int k){
        HashMap<Integer, ArrayList<Integer>> hm=new HashMap<Integer, ArrayList<Integer>>();
        HashMap<Integer, ArrayList<Integer>> hm1=new HashMap<Integer, ArrayList<Integer>>();
        for(int i=0;i<nums.size();i++){
            if(!hm.containsKey(nums.get(i))){
                hm.put(nums.get(i), new ArrayList<Integer>());
            }
            hm.get(nums.get(i)).add(i);
        }
        for(Map.Entry<Integer, ArrayList<Integer>> entry:hm.entrySet()){
            int key=entry.getKey();
            ArrayList<Integer> value=entry.getValue();
            ArrayList<Integer> arr=new ArrayList<Integer>();
            for(int i=1;i<value.size();i++){
                arr.add(value.get(i)-value.get(i-1)-1);
            }
            arr.add(k+1);
            hm1.put(key, new ArrayList<Integer>(arr));
        }
        // System.out.println(hm1);
        int ans=1;
        for(Map.Entry<Integer, ArrayList<Integer>> entry:hm1.entrySet()){
            ArrayList<Integer> value=entry.getValue();
            int i=0, j=0, temp=0, sum=0;
            while(j<value.size()){
                // System.out.println("sum: "+sum);
                if((sum)<=k){
                    // System.out.println(sum+value.get(j)+" "+value.get(j)+" "+sum);
                    ++temp;
                    sum+=value.get(j);
                    // System.out.println(entry.getKey()+" "+value.get(j)+" "+sum+" "+temp+" "+j);
                    ++j;
                }
                else{
                    // System.out.println("---");
                    ans=Math.max(ans, temp);
                    sum-=value.get(i);
                    // sum=Math.max(sum,0);
                    ++i;
                    // System.out.println("---> "+value.size());
                    --temp;
                }
            }
            ans=Math.max(ans, temp);
        }
        return ans;
    }
    int answer2(List<Integer> nums, int k){
        List<Integer> slidingWindow=new ArrayList<Integer>();
        for(int i=0;i<nums.size();i++){
            slidingWindow.add(-1);
            for(int j=i+1;j<=Math.min(nums.size()-1, i+k+1); j++){
                if(nums.get(i)==nums.get(j)){
                    slidingWindow.set(slidingWindow.size()-1, j-i-1);
                    break;
                }
            }
        }
        System.out.println(slidingWindow);
        int ans=0;
        for(int i=0;i<slidingWindow.size();i++){
            int c=i, a=1, tempK=k;
            while(c<slidingWindow.size() && slidingWindow.get(c)!=-1 && tempK-slidingWindow.get(c)>=0){
                ++a;
                tempK-=slidingWindow.get(c);
                c+=slidingWindow.get(c)+1;
            }
            ans=Math.max(ans, a);
        }
        return ans;
    }
}


````
</details>
<details>
<summary><a href="https://leetcode.com/problems/check-if-strings-can-be-made-equal-with-operations-ii/">Check if Strings Can be Made Equal With Operations II</a></summary>
					The question, is similar to the I variation of the question, with just a fun twist, instead of i+2 and i-2 position, this time, the dirrerence of swapped elements should be even ! A small observation, solves the problem, see, if an odd no. is added with an even no. it will always result in and odd no. itself, and for even no. it will always result in an even no. therefore, we don't need the place of the alphabet, just to know, is it in odd or even, and then substract its freq. simple! Felt, really good, when I came up with the solution !! Lol.


````java
class Solution {
    public boolean checkStrings(String s1, String s2) {
        HashMap<Character, OddEven> hm=new HashMap<Character, OddEven>();
        for(int i=0;i<s1.length();i++){
            if( !hm.containsKey(s1.charAt(i)) ){
                hm.put(s1.charAt(i), createOddEvenObj());   
            }
            if(i%2==0){
                ++hm.get(s1.charAt(i)).even;   
            }
            else{
                ++hm.get(s1.charAt(i)).odd;
            }
        }
        for(int i=0;i<s2.length();i++){
            // System.out.println(s2.charAt(i)+" "+hm);
            if(!hm.containsKey(s2.charAt(i)))return false;
            // System.out.println("--");
            
            //  even case
            if(i%2==0){
                if(hm.get(s2.charAt(i)).even>0){
                    --hm.get(s2.charAt(i)).even;
                }
                else{
                    return false;
                }
            }
            //  odd case
            else{
                if(hm.get(s2.charAt(i)).odd>0){
                    --hm.get(s2.charAt(i)).odd;
                }
                else{
                    return false;
                }
            }
        }
        return true;
    }
    OddEven createOddEvenObj(){
        return new OddEven();
    }
}
class OddEven{
    int odd=0, even=0;
}
````
</details>
<details>
<summary><a href="https://leetcode.com/problems/maximum-sum-of-almost-unique-subarray/">Maximum Sum of Almost Unique Subarray</a></summary>
					Well, it’s a simple, sliding window problem.

````java
class Solution {
    public long maxSum(List<Integer> nums, int m, int k) {
        HashMap<Integer, Integer> hm=new HashMap<Integer, Integer>();
        int i=0, j=i+k; long sum=0, ans=0;
        while(i<j){
            hm.put(nums.get(i), hm.getOrDefault(nums.get(i),0)+1);
            sum+=nums.get(i);
            ++i;
        }
        i=0;--j;
        while(j<nums.size()){
            if(hm.size()>=m){
                ans=Math.max(ans, sum);
            }   
            
            // System.out.println("j:"+nums.get(j)+" i:"+nums.get(i)+" sum:"+sum);
            // remove the i part
            sum-=nums.get(i);
            hm.put(nums.get(i), hm.get(nums.get(i))-1);
            if(hm.get(nums.get(i))==0){
                hm.remove(nums.get(i));
            }
            ++i;++j;
            if(j==nums.size())break;
            
            // add the j part
            hm.put(nums.get(j), hm.getOrDefault(nums.get(j),0)+1);
            sum+=nums.get(j);
        }
        return ans;
    }
}


````
</details>
<details>
<summary><a href="https://leetcode.com/problems/minimum-operations-to-make-a-special-number/">Minimum Operations to Make a Special Number</a></summary>
					It’s a simple thought experiment problem, really. If you know, the property of 25 multiplication, like nos. divisible by 25, ends with 00, 25,50,75, this question becomes pretty easy.

````java
class Solution {
    public int minimumOperations(String num) {
        return Math.min(forFive(num), forZero(num));
    }
    int forFive(String num){
        int i=0, j=num.length()-1;
        while(j>=0){
            if(num.charAt(j)=='5'){
                break;
            }
            else{
                --j;
            }
        }
        i=j-1;
        while(i>=0){
            if(num.charAt(j)=='0'){
                if(num.charAt(i)=='5' || num.charAt(i)=='0'){
                    break;
                }
            }
            else if(num.charAt(j)=='5'){
                if(num.charAt(i)=='2' || num.charAt(i)=='7'){
                    break;
                }
            }
            --i;
        }
        // System.out.println(i+" "+j);
        if(i==-1)return num.length();
        else{
            return (j-i-1)+(num.length()-j-1);
        }
    }
    
    int forZero(String num){
        int i=0, j=num.length()-1;
        while(j>=0){
            if(num.charAt(j)=='0'){
                break;
            }
            else{
                --j;
            }
        }
        i=j-1;
        while(i>=0){
            if(num.charAt(j)=='0'){
                if(num.charAt(i)=='5' || num.charAt(i)=='0'){
                    break;
                }
            }
            else if(num.charAt(j)=='5'){
                if(num.charAt(i)=='2' || num.charAt(i)=='7'){
                    break;
                }
            }
            --i;
        }
        // System.out.println(i+" "+j);
        if(i==-1)return num.length()-1;
        else{
            return (j-i-1)+(num.length()-j-1);
        }
    }
}



````
</details>
<details>
<summary><a href="https://leetcode.com/problems/longest-substring-with-at-least-k-repeating-characters/description/">Longest Substring with At Least K Repeating Characters</a></summary>
					Easy to understand, however, a bit tricky to implement. Experimented a lot here. This problem can be solved in various ways, like sliding window, I used divide and conquer. There are better and smart ways, to deal with this problem than mine.

````java
class Solution {
    public int longestSubstring(String s, int k) {
        // return answer1(s,k);
        return answer2(s,k);
        // return answer3(s,k);
    }
    int answer3(String s, int k){
        int arr[]=new int[26];
        for(int i=0;i<s.length();i++){
            char c=s.charAt(i);
            ++arr[c-'a'];
        }
        HashSet<Character> ignoreTheseNutz=new HashSet<Character>();
        HashSet<Integer> ignoreTheseIndexes=new HashSet<Integer>();
        for(int i=0;i<26;i++){
            if(arr[i]<k){
                char c=(char)('a'+i);
                ignoreTheseNutz.add(c);
            }
        }
        for(int i=0;i<s.length();i++){
            if(ignoreTheseNutz.contains(s.charAt(i))){
                ignoreTheseIndexes.add(i);
            }
        }
        ignoreTheseIndexes.add(s.length());
        int ans=0, prev=0; arr=new int[26];
        for(int i=0;i<=s.length();i++){
            if(ignoreTheseIndexes.contains(i)){
                boolean flag=true;
                for(int i1=0;i1<26;i1++){
                    if(arr[i1]==0)continue;
                    if(arr[i1]<k){
                        flag=false;
                        break;
                    }
                }
                if(!flag){
                    // System.out.println("i: "+Arrays.toString(arr));
                    ans=Math.max(ans, answer1(s.substring(prev, i), k));
                }
                else{
                    // System.out.println("i: "+i+" "+Arrays.toString(arr));
                    ans=Math.max(ans, i-prev);
                }
                prev=i+1;
                arr=new int[26];
            }
            else{
                ++arr[s.charAt(i)-'a'];
            }
        }
        return ans;
    }
    int answer2(String s, int k){
        int ans=0;
        for(int unique=1;unique<=26;unique++){
            HashMap<Character, Integer> currentUnique=new HashMap<Character, Integer>();
            int windowStart=0, windowEnd=0;
            int countAtleastK=0;
            while(windowEnd<s.length()){
                if(currentUnique.size()<=unique){
                    char temp=s.charAt(windowEnd);
                    currentUnique.put(temp, currentUnique.getOrDefault(temp, 0)+1);
                    if(currentUnique.get(temp)==k){
                        ++countAtleastK;
                    }
                    windowEnd++;
                }
                else{
                    char temp=s.charAt(windowStart);
                    if(currentUnique.get(temp)==k){
                        --countAtleastK;
                    }
                    currentUnique.put(temp, currentUnique.get(temp)-1);
                    if(currentUnique.get(temp)==0){
                        currentUnique.remove(temp);
                    }
                    windowStart++;
                }
                // System.out.println(currentUnique+" "+countAtleastK);
                if(currentUnique.size()==unique && countAtleastK==unique){
                    ans=Math.max(ans, windowEnd-windowStart);
                    // System.out.println(windowStart+" "+windowEnd+" "+countAtleastK+" "+currentUnique+" "+unique+" | "+ans);
                }
            }
        }
        return ans;
    }
    int answer1(String s, int k){
        int ans=0;
        for(int i=0;i<s.length();i++){
            int arr[]=new int[26];
            for(int j=i;j<s.length();j++){
                char temp=s.charAt(j);
                ++arr[temp-'a'];
                if(arr[temp-'a']>=k){
                    boolean flag=true;
                    for(int c=0;c<26;c++){
                        if(arr[c]==0)continue;
                        if(arr[c]<k){
                            flag=false;
                            break;
                        }
                    }
                    if(flag){
                        ans=Math.max(ans, j-i+1);
                    }
                }
            }
        }
        return ans;
    }
}


````
</details>
<details>
<summary><a href="https://leetcode.com/problems/count-k-subsequences-of-a-string-with-maximum-beauty/description/">Count K-Subsequences of a String With Maximum Beauty</a></summary>
					An easy to understand question, somewhat challenging. The first key observation is, k can't be greater than 26. So, find the frequency of arrays, and then use recursion or dp to solve it. You can use lots of other ways. Below is just a snapshot, of the different ways, I have experimented to solve.

````java
class Solution {
    HashMap<Character,Integer> hm;
    int maxValue;
    static int mod=1000000007;
    public int countKSubsequencesWithMaxBeauty(String s, int k) {
        /*brute force*/
        // return answer1(s,k);

        /*memoise*/
        // return answer2(s,k);

        // return answer3(s,k);

        // return answer4(s,k);

        return answer5(s,k);
    }
    long dpp[][];
    int answer5(String s, int k){
        if(k>26)return 0;
        int arr[]=new int[26];
        for(int i=0;i<s.length();i++){
            ++arr[s.charAt(i)-'a'];
        }
        Arrays.sort(arr);
        dpp=new long[26][27];
        reverse(arr);
        long answer=1; int taken=0;
        for(int i=0;i<k;i++){
            if(arr[i]==arr[k-1]||arr[i]==0)continue;
            answer=(answer*arr[i])%mod;
            ++taken;
        }
        for(int i=0;i<arr.length;i++){
            if(arr[i]!=arr[k-1]){
                arr[i]=0;
            }
        }
        // System.out.println(Arrays.toString(arr)+" "+answer);
        long ans= ((xyz5(arr, 0, k-taken)%mod)*(answer%mod))%mod;
        // System.out.println(ans);
        return (int)ans%mod;
    }
    void reverse(int arr[]){
        for(int i=0;i<arr.length/2;i++){
            int temp=arr[i];
            arr[i]=arr[arr.length-i-1];
            arr[arr.length-i-1]=temp;
        }
    }
    long xyz5(int arr[], int index, int k){
        // System.out.println(index+" "+k);
        if(index>=26){
            return k==0?1:0;
        }
        if(k<0||arr.length-index<k){
            return 0;
        }
        if(arr[index]==0){
            return xyz5(arr, index+1, k);
        }
        if(dpp[index][k]!=0)return dpp[index][k]%mod;
        dpp[index][k] =((xyz5(arr, index+1, k-1)*arr[index])%mod+xyz5(arr, index+1, k)%mod)%mod;
        return dpp[index][k]%mod;
    }
    long dp[][][];
    int answer4(String s, int k){
        if(k>26 || k>s.length()){
            return 0;
        }
        int arr[]=new int[26];
        for(int i=0;i<s.length();i++){
            ++arr[s.charAt(i)-'a'];
        }
        hm=new HashMap<Character, Integer>();
        for(char i:s.toCharArray()){
            hm.put(i, hm.getOrDefault(i, 0)+1);
        }
        maxValue=findMaxValue(k);
        dp=new long[26][27][8000];
        for(long d[][]:dp)
        for(long xyz[]:d){
            for(int i=0;i<xyz.length;i++){
                xyz[i]=-1;
            }
        }
        // System.out.println(maxValue+" "+Arrays.toString(arr));
        long answer=0;

        /* memoising with recursion type 1 */
        // answer=xyz41(0, k, arr, 0)%mod;
        
        // answer=xyz4(0, k, arr, 0)%mod;

        /* memoising with recursion type 2 */
        answer=xyz42(0, k, arr, 0)%mod;

        // System.out.println(s);
        return (int)answer;
    }
    long xyz4(int index, int k, int arr[], int sum){
        if(k<0 || arr.length-index<k){
            return 0;
        }
        if(sum==maxValue && k==0){
            return 1;
        }
        if(index>=26){
            return 0;
        }
        if(arr[index]==0){
            return xyz4(index+1, k, arr, sum)%mod;
        }
        return ((xyz4(index+1, k-1, arr, sum+arr[index])*arr[index])%mod+xyz4(index+1, k, arr, sum)%mod)%mod;
    }
    long xyz41(int index, int k, int arr[], int sum){
        if(k<0 || arr.length-index<k){
            return 0;
        }
        if(sum==maxValue && k==0){
            return 1;
        }
        if(index>=26){
            return 0;
        }
        long ans=dp[index][k][sum];
        // if(dp[index][k]!=-1){
        //     return dp[index][k];
        // }
        if(ans!=-1)return ans;
        if(arr[index]==0){
            return xyz41(index+1, k, arr, sum)%mod;
        }
        ans= ((xyz41(index+1, k-1, arr, sum+arr[index])*arr[index])%mod + xyz41(index+1, k, arr, sum)%mod)%mod;
        return ans;
        // return ((xyz4(index+1, k-1, arr, sum+arr[index])*arr[index])%mod+xyz4(index+1, k, arr, sum)%mod)%mod;
    }
    long xyz42(int index, int k, int arr[], int sum){
        if(k<0 || arr.length-index<k){
            return 0;
        }
        if(sum==maxValue && k==0){
            return 1;
        }
        if(index>=26){
            return 0;
        }
        if(dp[index][k][sum]!=-1){
            return dp[index][k][sum];
        }
        if(arr[index]==0){
            return xyz42(index+1, k, arr, sum)%mod;
        }
        dp[index][k][sum]= ((xyz42(index+1, k-1, arr, sum+arr[index])*arr[index])%mod + xyz42(index+1, k, arr, sum)%mod)%mod;
        return dp[index][k][sum];
    }
    int answer3(String s, int k){
        hm=new HashMap<Character, Integer>();
        for(char i:s.toCharArray()){
            hm.put(i, hm.getOrDefault(i, 0)+1);
        }
        maxValue=findMaxValue(k);
        TreeMap<Integer, ArrayList<Character>> tm=new TreeMap<Integer, ArrayList<Character>>();
        for (Map.Entry<Character, Integer> entry : hm.entrySet()) {
            char temp=entry.getKey();
            int cc=entry.getValue();
            if(!tm.containsKey(cc)){
                tm.put(cc, new ArrayList<Character>());
            }
            tm.get(cc).add(temp);
        }
        HashSet<Character> toBeConsidered=new HashSet<Character>();
        int c=0, key=tm.lastKey();
        while(c<k){
            // System.out.println(toBeConsidered+" "+c);
            toBeConsidered.addAll(tm.get(key));
            c+=tm.get(key).size();
            if(tm.lowerKey(key)==null)break;
            key=tm.lowerKey(key);
        }
        // System.out.println(toBeConsidered);
        return xyzAnswer3(s, 0, k, toBeConsidered, new HashSet<Character>(), 0);
        // return xyz(s, 0, k, new HashSet<Character>(), 0);
    }
    int ddp[][];
    int answer2(String s, int k){
        hm=new HashMap<Character, Integer>();
        for(char i:s.toCharArray()){
            hm.put(i, hm.getOrDefault(i, 0)+1);
        }
        maxValue=findMaxValue(k);
        ddp=new int[150][150];
       
    //    for(int i[][]:dp)
        for(int j[]:ddp){
            for(int k1=0;k1<j.length;k1++){
                j[k1]=-1;
            }
        }
        return xyz1(s, 0, k, new HashSet<Character>(), 0);
    }
    int xyz1(String s, int currIndex, int k, HashSet<Character> alreadyOccuredCharacters, int currentValue){
        if(maxValue==currentValue && k==0){
            return 1;
        }
        if(k==0){
            return 0;
        }
        if(currIndex>=s.length()){
            return 0;
        }
        if(alreadyOccuredCharacters.contains(s.charAt(currIndex))){
            return xyz(s, currIndex+1, k, alreadyOccuredCharacters, currentValue);
        }
        int ans=ddp[k][currentValue];
        if(ans!=-1)return ans;
        // if(ddp[k][currentValue]!=-1)return ddp[k][currentValue];
        HashSet<Character> temp=new HashSet<Character>(alreadyOccuredCharacters);
        temp.add(s.charAt(currIndex));
        // ddp[k][currentValue]= xyz1(s, currIndex+1, k-1, temp, currentValue+hm.get(s.charAt(currIndex))) + xyz1(s, currIndex+1, k, alreadyOccuredCharacters, currentValue);
        // return ddp[k][currentValue];

        ans= xyz1(s, currIndex+1, k-1, temp, currentValue+hm.get(s.charAt(currIndex))) + xyz1(s, currIndex+1, k, alreadyOccuredCharacters, currentValue);
        return ans;
    }
    int answer1(String s, int k){
        hm=new HashMap<Character, Integer>();
        for(char i:s.toCharArray()){
            hm.put(i, hm.getOrDefault(i, 0)+1);
        }
        maxValue=findMaxValue(k);
        return xyz(s, 0, k, new HashSet<Character>(), 0);
    }
    int findMaxValue(int k){
        ArrayList<ArrayList<Integer>> arr=new ArrayList<>();
        for (Map.Entry<Character, Integer> entry : hm.entrySet()) {
            ArrayList<Integer> temp=new ArrayList<Integer>();
            temp.add(entry.getKey()-'a');
            temp.add(entry.getValue());
            arr.add(temp);
        }
        Collections.sort(arr, (a,b)->(-a.get(1)+b.get(1)));
        int sum=0;
        for(int i=0;i<Math.min(k,arr.size());i++){
            sum+=arr.get(i).get(1);
        }
        return sum;
    }
    String getCharacterString(HashMap<Character, Integer> hm){
        String s="";
        for (Map.Entry<Character, Integer> entry : hm.entrySet()) {
            s+=entry.getKey();
        }
        return s;
    }
    int xyz(String s, int currIndex, int k, HashSet<Character> alreadyOccuredCharacters, int currentValue){
        if(maxValue==currentValue && k==0){
            return 1;
        }
        if(k==0){
            return 0;
        }
        if(currIndex>=s.length()){
            return 0;
        }
        if(alreadyOccuredCharacters.contains(s.charAt(currIndex))){
            return xyz(s, currIndex+1, k, alreadyOccuredCharacters, currentValue);
        }
        HashSet<Character> temp=new HashSet<Character>(alreadyOccuredCharacters);
        temp.add(s.charAt(currIndex));
        return xyz(s, currIndex+1, k-1, temp, currentValue+hm.get(s.charAt(currIndex))) + xyz(s, currIndex+1, k, alreadyOccuredCharacters, currentValue);
    }
    int xyzAnswer3(String s, int currIndex, int k, HashSet<Character> toBeConsidered, HashSet<Character> alreadyOccuredCharacters, int currentValue){
        if(maxValue==currentValue && k==0){
            return 1;
        }
        if(!toBeConsidered.contains(s.charAt(currIndex))){
            return xyz(s, currIndex+1, k, alreadyOccuredCharacters, currentValue);
        }
        if(k==0){
            return 0;
        }
        if(currIndex>=s.length()){
            return 0;
        }
        if(alreadyOccuredCharacters.contains(s.charAt(currIndex))){
            return xyz(s, currIndex+1, k, alreadyOccuredCharacters, currentValue);
        }
        HashSet<Character> temp=new HashSet<Character>(alreadyOccuredCharacters);
        temp.add(s.charAt(currIndex));
        return xyz(s, currIndex+1, k-1, temp, currentValue+hm.get(s.charAt(currIndex))) + xyz(s, currIndex+1, k, alreadyOccuredCharacters, currentValue);
    }
}


````
</details>
<details>
<summary><a href="https://leetcode.com/problems/minimum-operations-to-form-subsequence-with-target-sum/description/">Minimum Operations to Form Subsequence With Target Sum</a></summary>
					Well, since, the array contains only power of 2, values, you can assume, only the nos. greater than the array sum, aren't eligible. After this, sort the array, then substract and see, if you can match the target or not. If you face a road block, then, divide the no. You can also, use bitwise to solve it, basically, for the target, try getting the 1's and 0's from the array! <br>Below are my implementations:

````java
class Solution {
    public int minOperations(List<Integer> nums, int target) {
        // return answer1(nums, target);
        return answer2(nums, target);
    }
    int answer2(List<Integer> nums, int target){
        if(nums.stream().mapToLong(Integer::longValue).sum()<target)return -1;
        int arr[]=new int[32];
        for(int i:nums){
            ++arr[(int)(Math.log(i)/Math.log(2))];
        }
        int res=0;
        for(int i=0;i<31;i++,target=target>>1){
            // System.out.println(target);
            if((target&1)==1){
                if(arr[i]>0){
                    --arr[i];
                }
                else{
                    res+=getJob(arr,i);
                }
            }
            arr[i+1]+=arr[i]/2;
        }
        return res;
    }
    int getJob(int arr[], int x){
        int i;
        for(i=x+1;i<31;i++){
            if(arr[i]>0){
                --arr[i];
                break;
            }
        }
        for(int j=x;j<i;j++){
            ++arr[j];
        }
        return i-x;
    }
    int answer1(List<Integer> nums, int target){
        Collections.sort(nums);
        long tot=nums.stream().mapToLong(Integer::longValue).sum();
        Stack<Integer> st=new Stack<Integer>();
        for(int i:nums){
            st.push(i);
        }
        int res=0;
        if(tot<target)return -1;
        while(target>0){
            // System.out.println(res+" "+st+" "+tot);
            int a=st.pop();
            if(tot-a<target){
                if(a>target){
                    st.push(a/2);
                    st.push(a/2);
                    ++res;
                }
                else{
                    target-=a;
                    tot-=a;
                }
            }
            else{
                tot-=a;
            }
        }
        return res;
    }
}


````
</details>
<details>
<summary><a href="https://leetcode.com/problems/count-of-interesting-subarrays/description/">Count of Interesting Subarrays</a></summary>
Good question, with a little bit of familiarity of prefix sum, it can be solved. The bruteforece is quiet intuitive, that you store all the ith values, that fulfils the equation then use double loops to see, which fulfils the second condition and add the answers. In order to remove the double loop, just a thought that, the cnt is added and then moduloed, so, if we store it in hashmap, this essentially fulfils the purpose of double loops, and converting to single loop. This explanation, might seem vague, I suggest, to draw it, and you will be able to figure it out.

````java
class Solution {
    public long countInterestingSubarrays(List<Integer> nums, int modulo, int k) {
        /*brute force, without calculating prefix sum, just two arrays, calculating sum and checking that sum*/
        // return method1(nums, modulo, k);
        /*making a prefix sum, then (prefix[r]-prefix[l-1])% module ==k, count them*/
        // return method3(nums, modulo, k);
        /*rearranging the above equation, to, (prefix[r]%modulo)=(prefix[l-1]+k)%modulo*/
        // return method4(nums, modulo, k);
        return method5(nums, modulo, k);
    }
    long method4(List<Integer> nums, int modulo, int k){
        int arr[]=new int[nums.size()];
        // create array with the condition satisfying and not satisfying condition
        for(int i=0;i<nums.size();i++){
            arr[i]=nums.get(i)%modulo==k?1:0;
        }
        // create a prefix array
        long prefix[]=new long[nums.size()+1];
        for(int i=1;i<prefix.length;i++){
            prefix[i]=prefix[i-1]+arr[i-1];
        }
        long cnt=0;
        // System.out.println(Arrays.toString(prefix));
        for(int r=prefix.length-1;r>=0;r--){
            for(int l=0;l<r;l++){
                long sum2=(k+prefix[l])%modulo;
                if(sum2==prefix[r]%modulo){
                    ++cnt;
                }
            }
        }
        return cnt;
    }
    long method5(List<Integer> nums, int modulo, int k){
        int arr[]=new int[nums.size()];
        // create array with the condition satisfying and not satisfying condition
        for(int i=0;i<nums.size();i++){
            arr[i]=nums.get(i)%modulo==k?1:0;
        }
        // create a prefix array
        long prefix[]=new long[nums.size()+1];
        for(int i=1;i<prefix.length;i++){
            prefix[i]=prefix[i-1]+arr[i-1];
        }
        long cnt=0;
        
        HashMap<Long, Long> hm=new HashMap<Long, Long>();
        // for(int l=0;l<prefix.length;l++){
        //     long sum2=(k+prefix[l])%modulo;
        //     hm.put(sum2, hm.getOrDefault(sum2, 0l)+1);
        // }
        for(int r=0;r<prefix.length;r++){
            long sum1=prefix[r]%modulo;
            cnt+=hm.getOrDefault(sum1,0l);
            long sum2=(k+prefix[r])%modulo;
            hm.put(sum2, hm.getOrDefault(sum2, 0l)+1);
        }
        // System.out.println(Arrays.toString(prefix));
        // for(int r=prefix.length-1;r>=0;r--){
        //     for(int l=0;l<r;l++){
        //         long sum2=(k+prefix[l])%modulo;
        //         if(sum2==prefix[r]%modulo){
        //             ++cnt;
        //         }
        //     }
        // }
        return cnt;
    }
    long method3(List<Integer> nums, int modulo, int k){
        int arr[]=new int[nums.size()];
        // create array with the condition satisfying and not satisfying condition
        for(int i=0;i<nums.size();i++){
            arr[i]=nums.get(i)%modulo==k?1:0;
        }
        // create a prefix array
        long prefix[]=new long[nums.size()+1];
        for(int i=1;i<prefix.length;i++){
            prefix[i]=prefix[i-1]+arr[i-1];
        }
        long cnt=0;
        // System.out.println(Arrays.toString(prefix));
        for(int r=prefix.length-1;r>=0;r--){
            for(int l=0;l<r;l++){
                long sum1=prefix[r]-prefix[l];
                if(sum1%modulo==k){
                    ++cnt;
                }
            }
        }
        return cnt;
    }
    long method1(List<Integer> nums, int modulo, int k){
        List<Boolean> satisfies=new ArrayList<Boolean>();
        for(int i:nums){
            satisfies.add(i%modulo==k);
        }
        long ans=0;
        for(int i=0;i<nums.size();i++){
            long cnt=0; 
            for(int j=i;j<nums.size();j++){
                if(satisfies.get(j)){
                    ++cnt;
                }
                if(cnt%modulo==k){
                    ++ans;
                }
            }
        }
        return ans;
    }
}
````
</details>
<details>
<summary><a href="https://codeforces.com/contest/1872/problem/E">Data Structures Fan</a></summary>
					This is an advanced bitwise problem, although, understanding the problem is easy, and brute force method can be easily be enforced, however, its not easy to come up with the bitwise solution. Since, I had to have rounds with the editorial and multiple times, draw out the solution, in order to understand and arrive to the solution, I feel, its best, for people to view the official editorial and try drawing out the solution.


````java
import java.io.*;
import java.util.ArrayList;
import java.util.StringTokenizer;
import java.util.function.BiFunction;

public class DataStructureFan1 {
    public static void main(String[] args)throws IOException {
        String file="D:\\usaco.guide\\testFile.txt";
        BufferedReader x=new BufferedReader(new InputStreamReader(System.in));
//        BufferedReader x=new BufferedReader(new FileReader(file));
        int testCases=Integer.parseInt(x.readLine());
        while(testCases-->0){
            int n=Integer.parseInt(x.readLine());
            StringTokenizer st=new StringTokenizer(x.readLine());
            int xor0=0, xor1=0, preXor[]=new int[n+1], allXors=0;
            int arr[]=new int[n];
            for(int i=0;i<n;i++){
                arr[i]=Integer.parseInt(st.nextToken());
            }
            String s=x.readLine();
            for(int i=1;i<=n;i++){
                if('1'==s.charAt(i-1)){
                    xor1^=arr[i-1];
                }
                else{
                    xor0^=arr[i-1];
                }
                preXor[i]=preXor[i-1]^arr[i-1];
            }
            int query=Integer.parseInt(x.readLine());
            ArrayList<Integer> answers=new ArrayList<>();
            for(int i=0;i<query;i++){
                st=new StringTokenizer(x.readLine());
                int a=Integer.parseInt(st.nextToken());
                if(a==1){
                    int l=Integer.parseInt(st.nextToken());
                    int r=Integer.parseInt(st.nextToken());
                    allXors^=preXor[l-1]^preXor[r];
                }
                else {
                    int temp=Integer.parseInt(st.nextToken());
                    if(temp==1){
                        answers.add(xor1^allXors);
                    }
                    else{
                        answers.add(xor0^allXors);
                    }
                }
            }
            for(int i:answers){
                System.out.print(i+" ");
            }
            System.out.println();
        }
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
````
</details>

### Day 10: April 11, 2024

**Progress:**
Upsolved some of the problems, from the virtual contest (leetcode [Weekly Contest 391](https://leetcode.com/contest/weekly-contest-391/)) and Live Contest (Leetcode [Weekly Contest 392](https://leetcode.com/contest/weekly-contest-392/)), which I gave last week. In the virtual contest, was able to solve 2 problems, however, in the live contest, was only able to solve 1 problem, I guess, the nerves got me :(

**Thoughts:** The questions, in the contest were as follows:

***
***Virtual Contest***
<details>
<summary><a href="https://leetcode.com/problems/harshad-number/description/">Hashed Number</a></summary>
Solved it in the contest itself, was a very easy question. <br>
Implementation:

````java
class Solution {
    public int sumOfTheDigitsOfHarshadNumber(int x) {
        int temp=x, sumOfDigits=0;
        while(temp!=0){
            sumOfDigits+=temp%10;
            temp=temp/10;
        }
        if(x%sumOfDigits==0)return sumOfDigits;
        return -1;
    }
}
````
</details>
<details>
<summary><a href="https://leetcode.com/problems/water-bottles-ii/">Water Bottles II</a></summary>
Although, it was an easy question, however, wasn't able to solve it in the contest, I don't know why, I think, it was due to anxiety. It is pretty much a simulation problem, do whatever the question is instructing and you will easily arrive to the answer. <br>
Implementation:

````java
class Solution {
    public int maxBottlesDrunk(int numBottles, int numExchange) {
        int emptyBottles=0, totalBottlesDrunk=0;
        while(emptyBottles>=0){
            totalBottlesDrunk+=numBottles;
            emptyBottles+=numBottles;
            numBottles=0;
            emptyBottles-=numExchange;
            ++numBottles;
            ++numExchange;
        }
        return totalBottlesDrunk;
    }
}
````
</details>
<details>
<summary><a href="https://leetcode.com/problems/count-alternating-subarrays/description/">Count Alternating Subarrays</a></summary>
Not very easy, however, it takes time to see the pattern, but once you see it, can be easily done. Solved it in the contest. If you observe, if you find an alternating subarray, or take any alternating subarray, it can be divided further, although how much further ? If you draw some examples, then you will figure out, its sum of nos. from 1 to (length of array) i.e. assume, length of the array to be n, no of subarrays, that can be created is n*(n+1)/2. So, taking this observation, iterate through the loop adding the sum, whenever you encounter, there is equal no. and breaking the property of alternating subarray, store the sum and make the sum 0 and continue. After this step, use the formula, dipicted prior, for all the sums, stored in the array, and you are done! <br>
Implementation:

````java
class Solution {
    public long countAlternatingSubarrays(int[] nums) {
        // return solve(nums, i);
        long sum=0;
        ArrayList<Long> sums=new ArrayList<Long>();
        for(int i=1;i<nums.length;i++){
            if(nums[i-1]!=nums[i])++sum;
            else{
                sums.add(sum);
                sum=0;
            }
        }
        sums.add(sum);
        sum=0;
        long ans=0;
        for(long summ:sums){
            ans+=(summ*(summ+1))/2;
        }
        ans+=nums.length;
        return ans;
    }
    long solve(int nums[], int index){
        if(index==nums.length){
            return 0;
        }
        return 0;
        // if(index==0)
    }
}
````
</details>

***
***Live Contest***

<details>
<summary><a href="https://leetcode.com/problems/lexicographically-smallest-string-after-operations-with-constraint/description/">Lexicographically Smallest String After Operations With Constraint</a></summary>
Couldn't solve in the contest, however, worse, was easily able to solve after the contest, I guess, I have some kind of anxiety and nerves get the best of me. Moye Moye :(. So, the problem, is quite easy to understand, however, is a little bit tricky to implement. There is a simple observation, suppose, there is a strong "lzzzzzzzzzzz" and k=10, so, there are 10 z's in the string which can be easily converted to a, however, if we reduce l, by 10, we get 'c', so compare these two strings "czzzzzzzzzzz" and "laaaaaaaaaaa" both of them, updated by k value, needless to say, the first string is smaller. So, extrapolating this reasoning, we start with the 1st character of the string and try to reduce it to as low as possible. If k remains, we move ahead, else break the loop. If you understand this observation, with some hit and trial, you can arrive at the solution, with minimal difficulty.<br>
Implementation:

````java
class Solution {
    HashMap<Character,Integer> hm;
    public String getSmallestString(String s, int k) {   
        hm=new HashMap<Character,Integer>();     
        for(char i='a';i<='z';i++){
            hm.put(i,i-'a');
        }

        char arr[]=s.toCharArray();
        for(int i=0;i<arr.length;i++){
            int temp=minimumDistanceFrom_a(arr[i]);
            if(temp>k){
                arr[i]=(char)(arr[i]-k);
                break;
            }
            k=k-temp;
            arr[i]='a';
        }
        return new String(arr);
    }
    int minimumDistanceFrom_a(char c){
        int a=hm.get('z')-hm.get(c)+1;
        int b=hm.get(c);
        return Math.min(a,b);
    }
}
````
</details>
<details>
<summary><a href="https://leetcode.com/problems/minimum-operations-to-make-median-of-array-equal-to-k/description/">Minimum Operations to Make Median of Array Equal to K
</a></summary>
This problem was also, was unsolvable in the contest, however, to my fucking disappointment, I was able to solve it, right after the contest, that too, like the previous problem, within half an hour, coupled with, writing this update. I really, have to deal with the anxiety man!! The problem, essentially unwraps, based on a small observation. What's the medial ? Its nothing, but the center most element (as mentioned in the question). So, we have to sort the array, there is no other way. After which, the median given, we have to compare it with the median in the array. If the median is greater than the average, then, the elements, prior to the median is smaller, however, we need to check, if the elements post the median are still bigger or not. Traverse accordingly, and add the difference. Its best to simulate this on paper, and you can instantly view the simulation. Once you see it, its easily implementable  <br>
Implementation:

````java
class Solution {
    public long minOperationsToMakeMedianK(int[] nums, int k) {
        Arrays.sort(nums);
        int n=nums.length/2;
        long ans=Math.abs(nums[n]-k);
        // System.out.println(ans+" "+nums[n]+" "+k);
        if(k>nums[n]){
            for(int i=n+1;i<nums.length;i++){
                if(nums[i]<k){
                    ans+=(long)(k-nums[i]);
                }
                else{
                    break;
                }
            }
        }
        else{
            for(int i=n-1;i>=0;i--){
                if(nums[i]>k){
                    ans+=(long)(nums[i]-k);
                }
                else{
                    break;
                }
            }
        }
        return ans;
    }
}
````
</details>

***
### Day 11: April 12, 2024
**Progress:**
Attempted to upsolve the live contest as well as the virtual contest problem. I assumed, I would be able to solve both, however, was just able to solve one. Other than that, attempt a hard question, which I still can't wrap my head around, that question is [Omask Metro (Hard)](https://codeforces.com/problemset/problem/1843/F2)

**Thoughts:**

***Live Contest***
<details>
<summary><a href="https://leetcode.com/problems/minimum-cost-walk-in-weighted-graph/">Minimum Cost Walk in Weighted Graph</a></summary>
The problem looks frightening and complex, but the reality is a let down, once you observe some key tricks. So, the first trick, is, we have to find the max and, and the most confusing part is, even if you reach from point a to point b, then you can visit other nodes, to further reduce your answer. Well, that's the trick, if you find the lowest value, inside that component, then no matter what you and it with that value, it will either stay the same or reduce further, this is due the the property of and itself, which is true only when both the respective places are true. An off bit, can never be on. So keeping this in mind, we can assume, that, what ever is the total "and" for a component, it will be the lowest. So, keeping this in mind, we simply, find such components with dfs and find the component's and and store them. When we traverse the query, for the solution, then,we simply see, if they belong to the same component or not. If they belong to the same component, then we, give the answer, else, its unreachable!
<br>
Implementation:

````java
class Solution {
    HashMap<ArrayList<Integer>, Integer> edgeWeight;
    HashMap<Integer, ArrayList<Integer>> edge;
    public int[] minimumCost(int n, int[][] edges, int[][] query) {
        edge=new HashMap<Integer,ArrayList<Integer>>();
        edgeWeight=new HashMap<ArrayList<Integer>, Integer>();
        for(int e[]:edges){
            putInGraph(e);
            putInEdge(e);
        }
        HashSet<Integer> visited=new HashSet<Integer>();
        int componentIndex=1;
        int component[]=new int[n];
        int answer[]=new int[n];
        for(int i=0;i<n;i++){
            if(visited.contains(i))continue;
            Stack<Integer> st=new Stack<Integer>();
            ArrayList<Integer> tempArr=new ArrayList<Integer>();
            st.add(i);
            int componentAnd=0;
            boolean firstTimeFlag=true;
            while(!st.isEmpty()){
                int parentNode=st.pop();
                if(visited.contains(parentNode))continue;
                visited.add(parentNode);
                tempArr.add(parentNode);
                for(int currentNode:edge.getOrDefault(parentNode,new ArrayList<Integer>())){
                    if(firstTimeFlag){
                        firstTimeFlag=false;
                        componentAnd=getEdgeWeight(parentNode, currentNode);
                    }
                    else{
                        componentAnd=componentAnd&getEdgeWeight(parentNode,currentNode);
                    }
                    st.push(currentNode);
                }
            }
            for(int currentNode:tempArr){
                component[currentNode]=componentIndex;
                answer[currentNode]=componentAnd;
            }
            ++componentIndex;
        }
        int ans[]=new int[query.length];
        for(int i=0;i<query.length;i++){
            int a=query[i][0];
            int b=query[i][1];
            if(component[a]==component[b] && component[a]!=0){
                ans[i]=answer[a];
            }
            else{
                ans[i]=-1;
            }
        }
        return ans;
    }
    void putInGraph(int e[]){
        if(!edge.containsKey(e[0])){
            edge.put(e[0],new ArrayList<Integer>());
        }
        if(!edge.containsKey(e[1])){
            edge.put(e[1],new ArrayList<Integer>());
        }
        edge.get(e[0]).add(e[1]);
        edge.get(e[1]).add(e[0]);
    }
    void putInEdge(int e[]){
        ArrayList<Integer> temp1=new ArrayList<Integer>();
        temp1.add(e[0]);
        temp1.add(e[1]);
        if(edgeWeight.containsKey(temp1)){
            edgeWeight.put(temp1,edgeWeight.get(temp1)&e[2]);
        }
        else{
            edgeWeight.put(temp1,e[2]);
        }

        ArrayList<Integer> temp2=new ArrayList<Integer>();
        temp2.add(e[1]);
        temp2.add(e[0]);
        if(edgeWeight.containsKey(temp2)){
            edgeWeight.put(temp2,edgeWeight.get(temp1)&e[2]);
        }
        else{
            edgeWeight.put(temp2,e[2]);
        }
    }
    int getEdgeWeight(int a, int b){
        ArrayList<Integer> temp=new ArrayList<Integer>();
        temp.add(a);
        temp.add(b);
        return edgeWeight.get(temp);
    }
}
````

You can also, use Disjoint Set Union Datastructure, if you are familiar with it. Just reduces the complexity of writing code ie. makes the code more readable and faster. Although, the above one is also passing TC, at 100ms, which isn't a problem
<br>
Implementation:

````java
class Solution {
    int parent[];
    int size[];
    public int[] minimumCost(int n, int[][] edges, int[][] query) {
        parent=new int[n];
        size=new int[n];
        int ans[]=new int[n];
        Arrays.fill(ans,Integer.MAX_VALUE);
        Arrays.fill(size,1);
        for(int i=0;i<n;i++){
            parent[i]=i;
        }
        for(int i=0;i<edges.length;i++){
            int a=edges[i][0];
            int b=edges[i][1];
            int val=edges[i][2];
            int parentOf_a=getParent(a);
            int parentOf_b=getParent(b);
            if(parentOf_a!=parentOf_b){
                if(size[parentOf_a]<size[parentOf_b]){
                    parent[parentOf_a]=parentOf_b;
                    size[parentOf_b]+=size[parentOf_a];
                    ans[parentOf_b]=ans[parentOf_b]&ans[parentOf_a]&val;
                }
                else{
                    parent[parentOf_b]=parentOf_a;
                    size[parentOf_a]+=size[parentOf_b];
                    ans[parentOf_a]=ans[parentOf_a]&ans[parentOf_b]&val;
                }
            }
            else{
                ans[parentOf_a]=ans[parentOf_a]&val;
            }
        }
        int answer[]=new int[query.length];
        int c=0;
        for(int q[]:query){
            int a=q[0];
            int b=q[1];
            int parent_a=getParent(a);
            int parent_b=getParent(b);
            answer[c]=-1;
            if(parent_a==parent_b){
                answer[c]=ans[parent_a];
            }
            ++c;
        }
        return answer;
    }
    int getParent(int x){
        if(x==parent[x]){
            return x;
        }
        return getParent(parent[x]);
    }
}
````

</details>

***

### Day 12: April 13, 2024

**Progress:**
Went all in, to solve [Omask Metro (Hard)](https://codeforces.com/problemset/problem/1843/F2) and upsolve previous contest problem [Minimize Manhattan Distances](https://leetcode.com/problems/minimize-manhattan-distances/description/). Tried all my ideas, however, wasn't able to pass the TCs. Have started to get help of editorials. Its beneficial to know, where the holes in your understanding lies! Will soon update, if I am able to solve them.

### Day 13: April 14, 2024

**Progress:**
Well tried again, today, however, not with that intensity, to solve [Omask Metro (Hard)](https://codeforces.com/problemset/problem/1843/F2), but lost the battle, however, came pretty close, it won't stand long. However, went Gengis Khan, to upsolve previous contest problem [Minimize Manhattan Distances](https://leetcode.com/problems/minimize-manhattan-distances/description/) and finally conquered it! Not only that, did a complete analysis, as to where were the holes in my understanding and where I went wrong and what I did right, because, after trying for a while, I arrived to the correct solution, however didn't know, where I went wrong previously and why this solution is not working, and the previous ones were not working!

**Thoughts:**
Really enjoyed solving this problem [Minimize Manhattan Distances](https://leetcode.com/problems/minimize-manhattan-distances/description/), learnt a lot of stuff, explaining with details, all my implementations! Hop in!

<details>
<summary>[WA] Implementation 1:</summary>
Didn't understand the question properly. I was plainly finding the minimum of max distance. However, I missed a small detail, that, I had to find this distance, remove one of the points, from the set of points.

````java
class Solution {
    public int minimumDistance(int[][] points) {
        // return xyz(points);
        int min=Integer.MAX_VALUE;
        for(int i=0;i<points.length;i++){
            int max=0;boolean flag=false;
            for(int j=0;j<points.length;j++){
                flag=true;
                max=Math.max(max, distance(points[i],points[j]));
            }
            if(flag)
                min=Math.min(min, max);
        }        
        return min;
    }
    int xyz(int points[][]){
        for(int i=0;i<points.length;i++){
            for(int j=i+1;j<points.length;j++){
                System.out.println(Arrays.toString(points[i])+"-->"+Arrays.toString(points[j])+" : "+distance(points[i],points[j]));
            }
            System.out.println();
        }        
        return 0;
    }
    int distance(int arr1[], int arr2[]){
        return Math.abs(arr1[0]-arr2[0])+Math.abs(arr1[1]-arr2[1]);
    }
}
````

</details>
<details>
<summary>[TLE] Implementation 2:</summary>
Corrected, the previous error. Removed each point, got the max distance between two points, and saved the minimum from those points.

````java
class Solution {
    public int minimumDistance(int[][] points) {
        // xyz(points);
        return method3(points);
    }
    int method2(int points[][]){
        Arrays.sort(points, (a,b)->b[0]-a[0]);
        for(int i[]:points){
            System.out.println(Arrays.toString(i));
        }
        Arrays.sort(points, (a,b)->b[1]-a[1]);
        System.out.println();
        for(int i[]:points){
            System.out.println(Arrays.toString(i));
        }
        return 0;
    }
    int method1(int points[][]){
        int min=Integer.MAX_VALUE;
        for(int i=0;i<points.length;i++){
            int max=0;boolean flag=false;int sum=0;
            for(int j=0;j<points.length;j++){
                if(i==j)continue;
                flag=true;
                sum+=distance(points[i],points[j]);
                System.out.println(Arrays.toString(points[i])+"-->"+Arrays.toString(points[j])+" : "+distance(points[i],points[j]));
                max=Math.max(max, distance(points[i],points[j]));
            }
            System.out.println(max+" "+sum);
            if(flag)
                min=Math.min(min, max);
        }
        return min;
    }
    int method3(int points[][]){
        int min=Integer.MAX_VALUE;
        for(int i=0;i<points.length;i++){
            int max=distancesum(points,i);
            min=Math.min(max,min);
            // System.out.println(Arrays.toString(points[i])+": "+max);
        }
        return min;
    }
    int distancesum(int points[][], int x){
        int sum = 0, max=0;
        for (int i = 0; i < points.length; i++){
            if(i==x)continue;
            for (int j = i + 1; j < points.length; j++){
                if(j==x)continue;
                int temp= (Math.abs(points[i][0] - points[j][0])
                        + Math.abs(points[i][1] - points[j][1]));
                max=Math.max(temp,max);
                sum += temp;
            }
        }
        return max;
    }
    int xyz(int points[][]){
        for(int i=0;i<points.length;i++){
            for(int j=i+1;j<points.length;j++){
                System.out.println(Arrays.toString(points[i])+"-->"+Arrays.toString(points[j])+" : "+distance(points[i],points[j]));
            }
            System.out.println();
        }
        return 0;
    }
    int distance(int arr1[], int arr2[]){
        return Math.abs(arr1[0]-arr2[0])+Math.abs(arr1[1]-arr2[1]);
    }
}
````

</details>
<details>
<summary>[TLE] Implementation 3:</summary>
Used priority queue, to optimize.

````java
class Solution {
    public int minimumDistance(int[][] points) {
        PriorityQueue<Integer> pq=new PriorityQueue<Integer>((a,b)->b-a);
        for(int i=0;i<points.length;i++){
            for(int j=i+1;j<points.length;j++){
                pq.add(distance(points[i], points[j]));
            }
        }
        int min=Integer.MAX_VALUE;
        for(int removeIndex=0; removeIndex<points.length; removeIndex++) {
            HashMap<Integer,Integer> hs=new HashMap<Integer,Integer>();
            PriorityQueue<Integer> p=new PriorityQueue<Integer>(pq);
            for(int i=0;i<points.length;i++){
                if(i==removeIndex)continue;
                int temp=distance(points[removeIndex], points[i]);
                hs.put(temp,hs.getOrDefault(temp,0)+1);
            }
            int temp=p.peek();
            // System.out.println(p);
            // System.out.println(hs);
            while(hs.containsKey(temp)){
                // System.out.println("-- "+temp);
                hs.put(temp,hs.get(temp)-1);
                if(hs.get(temp)==0){
                    hs.remove(temp);
                }
                p.poll();
                temp=p.peek();
                // System.out.println("--> "+temp);
            }
            // System.out.println(p);
            // System.out.println(hs);
            min=Math.min(min,p.peek());
            // System.out.println();
        }
        return min;
    }
    int distance(int arr1[], int arr2[]) {
        return Math.abs(arr1[0]-arr2[0])+Math.abs(arr1[1]-arr2[1]);
    }
}
````
</details>
<details>
<summary>[WA] Implementation 4:</summary>
Finally, checked out the code of some people, who have solved it, to look where, I am going wrong. Turns out, there was a massive hole, in my understanding! There is a trick to find the max manhattan distance, between two points, which takes o(n) time. Its easily available on the internet. Did a very bad implementation, which eventually caught up in a wrong answer. The imlementation, was so complex, eventuall, I did a fresh implementation and ditched debugging it.

````java
class Solution {
    int points[][];
    public int minimumDistance(int[][] pppp) {
        points=pppp;
        int minSum[]=new int[2];
        int maxSum[]=new int[2];
        int maxDiff[]=new int[2];
        int minDiff[]=new int[2];
        Arrays.fill(minSum,Integer.MAX_VALUE);
        Arrays.fill(minDiff,Integer.MAX_VALUE);
        for(int i=0;i<points.length;i++){
            int x=points[i][0];
            int y=points[i][1];
            int sum=x+y;
            int diff=x-y;
            if(minSum[0]>sum){
                update(minSum,sum,i);
            }
            if(maxSum[0]<sum){
                update(maxSum,sum,i);
            }
            if(maxDiff[0]<diff){
                update(maxDiff,diff,i);
            }
            if(minDiff[0]>diff){
                update(minDiff,diff,i);
            }
        }
        int[] maxManhattanDistance=max(diff(maxSum,minSum), diff(maxDiff,minDiff));
        // System.out.println(Arrays.toString(points[maxManhattanDistance[0]])+" "+Arrays.toString(points[maxManhattanDistance[1]]));
        return Math.min(MaxDist(points,maxManhattanDistance[0]), MaxDist(points, maxManhattanDistance[1]));
    }
    static int MaxDist(int[][] A, int x)
    {
        int N=A.length;
        // Variables to track running extrema
        int minsum=Integer.MAX_VALUE, 
        maxsum=Integer.MIN_VALUE, 
        mindiff=Integer.MAX_VALUE, 
        maxdiff=Integer.MIN_VALUE;
 
        for (int i = 0; i < N; i++) {
            if(i==x)continue;
            int sum = A[i][0] + A[i][1];
            int diff = A[i][0] - A[i][1];
            if (sum < minsum)
                minsum = sum;
            else if (sum > maxsum)
                maxsum = sum;
            if (diff < mindiff)
                mindiff = diff;
            else if (diff > maxdiff)
                maxdiff = diff;
        }
 
        int maximum
            = Math.max(maxsum - minsum, maxdiff - mindiff);
 
        return maximum;
    }
    int[] diff(int arr1[], int arr2[]){
        int arr[]=new int[3];
        arr[0]=arr1[1];
        arr[1]=arr2[1];
        arr[2]=arr1[0]-arr2[0];
        return arr;
    }
    int[] max(int[] a,int[] b){
        int arr[]=new int[2];
        if(a[0]>b[0]){
            arr[0]=a[0];
            arr[1]=a[1];
        }
        else{
            arr[0]=b[0];
            arr[1]=b[1];
        }
        return arr;
    }
    void update(int arr[], int sum, int index){
        arr[0]=sum;
        arr[1]=index;
    }
}
````
</details>
<details>
<summary>[TLE] Honerable Mention</summary>
This optimized code, for readability, eventually lead me to solution. 

````java
class Solution {
    int points[][];
    public int minimumDistance(int[][] pppp) {
        points=pppp;
        int minSum=getMinSum();
        int maxSum=getMaxSum();
        int minDiff=getMinDiff();
        int maxDiff=getMaxDiff();
        ArrayList<Integer> indexOfPoints=new ArrayList<Integer>();
        for(int i=0;i<points.length;i++){
            int x=points[i][0];
            int y=points[i][1];
            int sum=x+y;
            int diff=x-y;
            if(condition(sum, diff, minSum, maxSum, minDiff, maxDiff)){
                indexOfPoints.add(i);
            }
        }
        int min=Integer.MAX_VALUE;
        for(int i:indexOfPoints){
            int max=distancesum(i);
            min=Math.min(max,min);
        }
        return min;
    }
    int distancesum(int x){
        int sum = 0, max=0;
        for (int i = 0; i < points.length; i++){
            if(i==x)continue;
            for (int j = i + 1; j < points.length; j++){     
                if(j==x)continue;     
                int temp= (Math.abs(points[i][0] - points[j][0])
                        + Math.abs(points[i][1] - points[j][1]));
                max=Math.max(temp,max);
                sum += temp;
            }
        }
        return max;
    }
    boolean condition(int sum, int diff, int minSum, int maxSum, int minDiff, int maxDiff){
        if(minSum==sum){
            return true;
        }
        if(maxSum==sum){
            return true;
        }
        if(maxDiff==diff){
            return true;
        }
        if(minDiff==diff){
            return true;
        }
        return false;
    }
    int getMinSum(){
        int ans=Integer.MAX_VALUE;
        for(int i=0;i<points.length;i++){
            int x=points[i][0];
            int y=points[i][1];
            int sum=x+y;
            if(ans>sum){
                ans=sum;
            }
        }
        return ans;
    }
    int getMaxSum(){
        int ans=0;
        for(int i=0;i<points.length;i++){
            int x=points[i][0];
            int y=points[i][1];
            int sum=x+y;
            if(ans<sum){
                ans=sum;
            }
        }
        return ans;
    }
    int getMinDiff(){
        int ans=Integer.MAX_VALUE;
        for(int i=0;i<points.length;i++){
            int x=points[i][0];
            int y=points[i][1];
            int diff=x-y;
            if(ans>diff){
                ans=diff;
            }
        }
        return ans;
    }
    int getMaxDiff(){
        int ans=0;
        for(int i=0;i<points.length;i++){
            int x=points[i][0];
            int y=points[i][1];
            int diff=x-y;
            if(ans<diff){
                ans=diff;
            }
        }
        return ans;
    }
}
````

</details>
<details>
<summary>[AC] Implementation</summary>
There were some more implementation, which were kind of experiments, which I didn't want to mention.

````java
class Solution {
    int points[][];
    public int minimumDistance(int[][] pppp) {
        points=pppp;
        int minSum=getMinSum();
        int maxSum=getMaxSum();
        int minDiff=getMinDiff();
        int maxDiff=getMaxDiff();
        ArrayList<Integer> indexOfPoints=new ArrayList<Integer>();
        for(int i=0;i<points.length;i++){
            int x=points[i][0];
            int y=points[i][1];
            int sum=x+y;
            int diff=x-y;
            if(condition(sum, diff, minSum, maxSum, minDiff, maxDiff)){
                indexOfPoints.add(i);
            }
        }
        int min=Integer.MAX_VALUE;

        for(int i:indexOfPoints){
            int temp=maxDist(i);
            // System.out.println(Arrays.toString(points[i])+" "+temp);
            min=Math.min(min, maxDist(i));
        }

        // for(int i:indexOfPoints){
        //     int A[][]=new int[points.length-1][2];
        //     int c=0;
        //     for(int x=0;x<points.length;x++){
        //         if(x==i)continue;
        //         A[c][0]=points[x][0];
        //         A[c][1]=points[x][1];
        //         ++c;
        //     }
        //     min=Math.min(min, maxDist(A));
        // }

        return min;
    }
    int maxDist(int A[][]){
        int N=A.length;

        // int minsum, maxsum, mindiff, maxdiff;
        // minsum = maxsum = A[0][0] + A[0][1];
        // mindiff = maxdiff = A[0][0] - A[0][1];

        int minsum=Integer.MAX_VALUE;
        int maxsum=Integer.MIN_VALUE;
        int mindiff=Integer.MAX_VALUE;
        int maxdiff=Integer.MIN_VALUE;
        // System.out.println(Arrays.deepToString(A));
        for (int i = 0; i < N; i++) {
            int sum = A[i][0] + A[i][1];
            int diff = A[i][0] - A[i][1];
            if (sum < minsum)
                minsum = sum;
            if (sum > maxsum)
                maxsum = sum;
            if (diff < mindiff)
                mindiff = diff;
            if (diff > maxdiff)
                maxdiff = diff;
            // System.out.println(maxsum+" "+maxdiff+" "+minsum+" "+mindiff);
        }
        // System.out.println();
        int maximum
                = Math.max(maxsum - minsum, maxdiff - mindiff);

        return maximum;
    }
    int maxDist(int x){
        int A[][]=points;
        int N=A.length;

        int minsum=Integer.MAX_VALUE;
        int maxsum=Integer.MIN_VALUE;
        int mindiff=Integer.MAX_VALUE;
        int maxdiff=Integer.MIN_VALUE;

        for (int i = 0; i < N; i++) {
            if(i==x)continue;
            int sum = A[i][0] + A[i][1];
            int diff = A[i][0] - A[i][1];
            if (sum < minsum)
                minsum = sum;
            if (sum > maxsum)
                maxsum = sum;
            if (diff < mindiff)
                mindiff = diff;
            if (diff > maxdiff)
                maxdiff = diff;
        }

        int maximum
                = Math.max(maxsum - minsum, maxdiff - mindiff);

        return maximum;
    }
    boolean condition(int sum, int diff, int minSum, int maxSum, int minDiff, int maxDiff){
        if(minSum==sum){
            return true;
        }
        if(maxSum==sum){
            return true;
        }
        if(maxDiff==diff){
            return true;
        }
        if(minDiff==diff){
            return true;
        }
        return false;
    }
    int getMinSum(){
        int ans=Integer.MAX_VALUE;
        for(int i=0;i<points.length;i++){
            int x=points[i][0];
            int y=points[i][1];
            int sum=x+y;
            if(ans>sum){
                ans=sum;
            }
        }
        return ans;
    }
    int getMaxSum(){
        int ans=0;
        for(int i=0;i<points.length;i++){
            int x=points[i][0];
            int y=points[i][1];
            int sum=x+y;
            if(ans<sum){
                ans=sum;
            }
        }
        return ans;
    }
    int getMinDiff(){
        int ans=Integer.MAX_VALUE;
        for(int i=0;i<points.length;i++){
            int x=points[i][0];
            int y=points[i][1];
            int diff=x-y;
            if(ans>diff){
                ans=diff;
            }
        }
        return ans;
    }
    int getMaxDiff(){
        int ans=0;
        for(int i=0;i<points.length;i++){
            int x=points[i][0];
            int y=points[i][1];
            int diff=x-y;
            if(ans<diff){
                ans=diff;
            }
        }
        return ans;
    }
}
````

</details>

### Day 14: April 15, 2024
**Progress:**
Gave a Leetcode contest [Weekly Contest 393], well, better than previous contest, was able to solve 2 problems, however couldn't solve the rest. Will, upsolve it, later this week.

**Thoughts:**
Details about the solved problems are:
<details>
<summary><a href="https://leetcode.com/problems/latest-time-you-can-obtain-after-replacing-characters/description/">Latest Time You Can Obtain After Replacing Characters</a></summary>
Well, it was a simple problem, was a bit tricky to implement. Looking at the constraints, and getting excited about the harder problems, I just wanted to solved it, and looking at the constraints, was pretty sure, an little optimized brute force solution will pass. and it did! because, more than running the program, i have only 1.5 hrs. to solve 4 questions, therefore, I have to invest as minimal time as possible on these easy problems <br>
My contest implementation:

````java
class Solution {
    public String findLatestTime(String s) {
        int c=0;
        HashMap<String,Integer> hm;
        hm=new HashMap<String,Integer>();
        for(int i=0;i<12;i++){
            for(int j=0;j<=59;j++){
                String hours="";
                String minutes="";
                if(i<10){
                    hours="0"+i;
                }
                else{
                    hours=i+"";
                }
                if(j<10){
                    minutes="0"+j;
                }
                else{
                    minutes=j+"";
                }
                String temp=hours+":"+minutes;
                hm.put(temp,c++);
            }
        }
        String answer="";
        int maxScore=-1;
        for(int i=0;i<12;i++){
            for(int j=0;j<=59;j++){
                String hours="";
                String minutes="";
                if(i<10){
                    hours="0"+i;
                }
                else{
                    hours=i+"";
                }
                if(j<10){
                    minutes="0"+j;
                }
                else{
                    minutes=j+"";
                }
                String temp=hours+":"+minutes;
                if(match(s,temp)){
                    int score=hm.get(temp);
                    if(maxScore<score){
                        score=maxScore;
                        answer=temp;
                    }
                }
            }
        }
        return answer;
    }
    boolean match(String hours, String temp){
        for(int i=0;i<hours.length();i++){
            if(hours.charAt(i)=='?')continue;
            if(hours.charAt(i)==temp.charAt(i))continue;
            // System.out.println(hours+" "+temp+" "+i+" "+hours.charAt(i)+" "+temp.charAt(i));
            return false;
        }
        // System.out.println(hours+" "+temp);
        return true;
    }
}
````
After contest, a more optimized decent implementation:

````java
class Solution {
    public String findLatestTime(String s) {
        for(int i=11;i>=0;i--){
            for(int j=59;j>=0;j--){
                String hours=i+"";
                String minutes=j+"";
                if(i<10){
                    hours="0"+hours;
                }
                if(j<10){
                    minutes="0"+minutes;
                }
                String temp=hours+":"+minutes;
                if(match(s,temp)){
                    return temp;
                }
            }
        }
        return "";
    }
    boolean match(String hours, String temp){
        for(int i=0;i<hours.length();i++){
            if(hours.charAt(i)=='?')continue;
            if(hours.charAt(i)==temp.charAt(i))continue;
            // System.out.println(hours+" "+temp+" "+i+" "+hours.charAt(i)+" "+temp.charAt(i));
            return false;
        }
        // System.out.println(hours+" "+temp);
        return true;
    }
}
````
Not listing the other crazy experiments I did on this problem, after contest.
</details>
<details>
<summary><a href="https://leetcode.com/problems/maximum-prime-difference/description/">Maximum Prime Difference</a></summary>
This was also an easy problem, I found it easier than the above problem. I have to identify the primes in the array and substract the index. How to find the primes? Well, either I can, calculate every no. to find if they are prime or not, or create a sieve, and then found out, the nos. have a limit till 100 however, the array of nos. is huge, so figured, why not store the prime nos. until 100 and proceed!<br>
My Contest Implementation:

````java
class Solution {
    public int maximumPrimeDifference(int[] nums) {
        HashSet<Integer> primeSieve=new HashSet<Integer>();
        int arr[]={2, 3, 5, 7, 11, 13, 17, 19, 23, 29, 31, 37, 41, 43, 47, 53, 59, 61, 67, 71, 73, 79, 83, 89, 97};
        for(int i:arr){
            primeSieve.add(i);
        }
        int highest=0, lowest=0;
        for(int i=0;i<nums.length;i++){
            if(primeSieve.contains(nums[i])){
                highest=i;
            }
        }
        for(int i=nums.length-1;i>=0;i--){
            if(primeSieve.contains(nums[i])){
                lowest=i;
            }
        }
        return highest-lowest;
    }
}
````
After contest implementation, where I am generating the prime nos. in somthing called Sieve method.

````java
class Solution {
    HashSet<Integer> primeSieve;
    public int maximumPrimeDifference(int[] nums) {
        createPrimeSieve();
        int highest=0, lowest=0;
        for(int i=nums.length-1;i>=0;i--){
            if(primeSieve.contains(nums[i])){
                highest=i;
                break;
            }
        }
        for(int i=0;i<nums.length;i++){
            if(primeSieve.contains(nums[i])){
                lowest=i;
                break;
            }
        }
        return highest-lowest;
    }
    void createPrimeSieve(){
        primeSieve=new HashSet<Integer>();
        for(int i=2;i<=100;i++){
            primeSieve.add(i);
        }
        for(int i=2;i<=100;i++){
            int c=2;
            while(i*c<=100){
                primeSieve.remove(i*c);
                ++c;
            }
        }
    }
}
````
if you observe closely, you can find the highest prime index, when you traverse from the right, and when you arrive at that, break the loop. Vice Versa for the prime at the smallest index. To find the prime, there is a pretty kickass efficient gcd algorithm which has a logarithmic time complexity! it can be easily used here and manage almost the same level of tc, since, each no. is capped at 100 itself.
</details>

### Day 15: April 16, 2024
 
Took a day off, to chill, would count Day 15, again, tomorrow. However, as the rules state, won't extend this, for more than a consecutive day

### Day 15: April 17, 2024

**Progress**: 
Trying to upsolve the contest problem. Wasn't able to solve 2 problems. Currently, trying to solve this problem [Kth Smallest Amount With Single Denomination Combination](https://leetcode.com/problems/kth-smallest-amount-with-single-denomination-combination/description/), the question, is very easy to understand, however, can't figure out, the edge cases, getting stuck in some test cases all the time. Still haven't seen the editorial, since I have some more ideas left, and until, I am out of ideas, I will continue to try.

Some of the implementations, are below (well, all of them are WA :-( )
<details>
<summary>Impementation 1</summary>

````java
class Solution {
    public long findKthSmallest(int[] coins, int k) {
        Arrays.sort(coins);
        if(coins[0]==1){
            return k;
        }
        else{
            
        }
        long kValue=coins[0]*k;
        // find all the unique contributions
        ArrayList<Long> uniqueContributions=new ArrayList<Long>();
        for(int i=1;i<coins.length;i++){
            long d=kValue/coins[i];
            for(int j=1;j<=d;j++){
                long val=coins[i]*j;
                boolean uniqueFlag=true;
                for(int c=0;c<i;c++){
                    if(val%coins[c]==0){
                        uniqueFlag=false;
                        break;
                    }
                }
                if(uniqueFlag){
                    uniqueContributions.add(val);
                }
            }
        }
        Collections.sort(uniqueContributions);
        for(long i:uniqueContributions){
            if(kValue<=i){
                break;
            }
            kValue=coins[0]*--k;
        }
        return kValue;
    }
}
````

</details>
<details>
<summary>Impementation 2</summary>

````java
class Solution {
    public long findKthSmallest(int[] coins, int k) {
        Arrays.sort(coins);
        if(coins[0]==1){
            return k;
        }
        else{

        }
        int kValue=coins[0]*k;
        for(int i=1;i<coins.length;i++){
            int d=kValue/coins[i];
            while(d>0){
                int val=coins[i]*d;
                boolean flag= true;
                for(int j=0;j<i;j++){
                    int c=coins[j];
                    if(val%c==0){
                        --d;
                        flag=false;
                        break;
                    }
                }
                if(flag){
                    int temp1=coins[0]*--k;
                    int temp2=coins[1]*d;
                    kValue=Math.max(temp1,temp2);
                    break;
                }
            }
        }
        return kValue;
    }
}
````
</details>

### Day 16: April 18, 2024

**Progress**:
Solved one of them, [Minimum Sum of Values by Dividing Array](https://leetcode.com/problems/minimum-sum-of-values-by-dividing-array/), was not that hard, solved within an hour, without even watching the editorial. This makes me sad, as, why wasn't I able to come up with the solution, in the contest.... Listing down the implementations, which led me to the solution

<details>
<summary>Brute Force [TLE]</summary>
Well, I knew, it would TLE, wanted to establish the recurrence relation, if its successfully, we just to memoize it, so that states are not visited to do the calculation again, instead, they can be cached. <br>

````java
class Solution {
    int nums[], andValues[];
    int MAX_VALUE=2*1000000;
    public int minimumValueSum(int[] n, int[] a) {
        nums=n; andValues=a;
        int ans=xyz(0,0,Integer.MAX_VALUE);
        if(ans==MAX_VALUE){
            return -1;
        }
        return ans;
    }
    int xyz(int numsIndex, int andValuesIndex, int currVal){
        if(numsIndex==nums.length && andValuesIndex==andValues.length){
            return 0;
        }
        else if(numsIndex==nums.length || andValuesIndex==andValues.length){
            return MAX_VALUE;
        }
        // System.out.println("(currVal:"+currVal+") & (nums:"+nums[numsIndex]+"): "+(currVal&nums[numsIndex])+" "+" andValuesIndex:"+andValuesIndex);
        if((currVal&nums[numsIndex])==andValues[andValuesIndex]){
            // System.out.println("--> (currVal:"+currVal+") & (nums:"+nums[numsIndex]+") : "+(currVal&nums[numsIndex])+" "+andValuesIndex);
            return Math.min(nums[numsIndex]+xyz(numsIndex+1,andValuesIndex+1,Integer.MAX_VALUE), xyz(numsIndex+1, andValuesIndex,(currVal&nums[numsIndex])));
        }
        else{
            return xyz(numsIndex+1, andValuesIndex,(currVal&nums[numsIndex]));
        }
    }
}
````
</details>
<details>
<summary>[AC] Correct!</summary>
Well, memoised the above, found the variables, which were controlling the states, and memoised it and voila!

````java
class Solution {
    int nums[], andValues[]; 
    HashMap<Integer, Integer> dpp[][];
    int MAX_VALUE=2*1000000;
    public int minimumValueSum(int[] n, int[] a) {
        nums=n; andValues=a;
        dpp=new HashMap[andValues.length][nums.length];
        for(int i=0;i<andValues.length;i++){
            for(int j=0;j<nums.length;j++){
                dpp[i][j]=new HashMap<Integer,Integer>();
            }
        }
        int ans=xyz2(0,0,Integer.MAX_VALUE);
        if(ans==MAX_VALUE){
            return -1;
        }
        return ans;
    }
    int xyz2(int numsIndex, int andValuesIndex, int currVal){
        if(numsIndex==nums.length && andValuesIndex==andValues.length){
            return 0;
        }
        else if(numsIndex==nums.length || andValuesIndex==andValues.length){
            return MAX_VALUE;
        }
        if(dpp[andValuesIndex][numsIndex].containsKey(currVal)){
            return dpp[andValuesIndex][numsIndex].get(currVal);
        }
        if((currVal&nums[numsIndex])==andValues[andValuesIndex]){
            dpp[andValuesIndex][numsIndex].put(currVal, Math.min(nums[numsIndex]+xyz2(numsIndex+1,andValuesIndex+1,Integer.MAX_VALUE), xyz2(numsIndex+1, andValuesIndex,(currVal&nums[numsIndex]))));
        }        
        else{
            dpp[andValuesIndex][numsIndex].put(currVal, xyz2(numsIndex+1, andValuesIndex,(currVal&nums[numsIndex])));
        }
        return dpp[andValuesIndex][numsIndex].get(currVal);
    }
}
````

</details>

### Day 17: April 19, 2024

**Progress**:
Came close to solving the second problem, [Kth Smallest Amount With Single Denomination Combination](https://leetcode.com/problems/kth-smallest-amount-with-single-denomination-combination/description/). Finally, found out a brute force solution and have gotten some hint, as to where the solution might be.... In the bruteforce method, I am considering all the nos. not unique in the set of coin denominations and storing them in the array. As the lowest coin, will have its kth value by default, insertion of the lowest value from the unique set, will result to decrease in the kth value, which is the last value of the above array and also add k-th value from the smallest coin. We have to do this, until, l<r. Well, the explanation, might be vague, I promise, the code, will be easier to understand. The storing in the array, sorting it, then adding it, and linearly finding it, seems to be a tedious operation. I think, apart from storing, we can do something about the sorting and the traversal, which can be cut down from liner to binary search (log n), lets see, will give it a try again!!! So excited!! My experiments are below:

<details>
<summary>[WA] implementation 1</summary>

````java
class Solution {
    public long findKthSmallest(int[] coins, int k) {
        Arrays.sort(coins);
        if(coins[0]==1){
            return k;
        }
        else{

        }
        int kValue=coins[0]*k;
        for(int i=1;i<coins.length;i++){
            int d=kValue/coins[i];
            while(d>0){
                int val=coins[i]*d;
                boolean flag= true;
                for(int j=0;j<i;j++){
                    int c=coins[j];
                    if(val%c==0){
                        --d;
                        flag=false;
                        break;
                    }
                }
                if(flag){
                    int temp1=coins[0]*--k;
                    int temp2=coins[1]*d;
                    kValue=Math.max(temp1,temp2);
                    break;
                }
            }
        }
        return kValue;
    }
}
````

</details>
<details>
<summary>[WA] implementation 2</summary>

````java
class Solution {
    public long findKthSmallest(int[] coins, int k) {
        Arrays.sort(coins);
        if(coins[0]==1){
            return k;
        }
        else{
            
        }
        long kValue=coins[0]*k;
        // find all the unique contributions
        ArrayList<Long> uniqueContributions=new ArrayList<Long>();
        for(int i=1;i<coins.length;i++){
            long d=kValue/coins[i];
            for(int j=1;j<=d;j++){
                long val=coins[i]*j;
                boolean uniqueFlag=true;
                for(int c=0;c<i;c++){
                    if(val%coins[c]==0){
                        uniqueFlag=false;
                        break;
                    }
                }
                if(uniqueFlag){
                    uniqueContributions.add(val);
                }
            }
        }
        Collections.sort(uniqueContributions);
        for(long i:uniqueContributions){
            if(kValue<=i){
                break;
            }
            kValue=coins[0]*--k;
        }
        return kValue;
    }
}
````

</details>
<details>
<summary>[WA] implementation 3</summary>

````java
class Solution {
    public long findKthSmallest(int[] coins, int k) {
        Arrays.sort(coins);
        long kValue=coins[0]*k;
        // find all the unique contributions
        ArrayList<Long> uniqueContributions=new ArrayList<Long>();
        for(int i=1;i<coins.length;i++){
            long d=kValue/coins[i];
            for(int j=1;j<=d;j++){
                long val=coins[i]*j;
                boolean uniqueFlag=true;
                for(int c=0;c<i;c++){
                    if(val%coins[c]==0){
                        uniqueFlag=false;
                        break;
                    }
                }
                if(uniqueFlag){
                    uniqueContributions.add(val);
                }
            }
        }
        if(uniqueContributions.size()==0){
            return coins[0]*k;
        }
        uniqueContributions.add((long)coins[0]*k);
        int insertElement=0, kthValue=uniqueContributions.size();
        // System.out.println(kthValue);
        while(insertElement<kthValue){
            // System.out.println(insertElement+" "+kthValue);
            // System.out.print(uniqueContributions);
            uniqueContributions.add((long)coins[0]*--k);
            --k;
            Collections.sort(uniqueContributions);
            ++insertElement;
            --kthValue;
            // System.out.println(" --> "+uniqueContributions);
        }
        // System.out.println(kthValue);
        return uniqueContributions.get(kthValue);
    }
}
````

</details>
<details>
<summary>[TLE] Proper Bruteforce Solution</summary>

````java
class Solution {
    public long findKthSmallest(int[] coins, int k) {
        Arrays.sort(coins);
        long kValue=coins[0]*k;
        // find all the unique contributions
        ArrayList<Long> uniqueContributions=new ArrayList<Long>();
        for(int i=1;i<coins.length;i++){
            long d=kValue/coins[i];
            for(int j=1;j<=d;j++){
                long val=coins[i]*j;
                boolean uniqueFlag=true;
                for(int c=0;c<i;c++){
                    if(val%coins[c]==0){
                        uniqueFlag=false;
                        break;
                    }
                }
                if(uniqueFlag){
                    uniqueContributions.add(val);
                }
            }
        }
        if(uniqueContributions.size()==0){
            return coins[0]*k;
        }
        uniqueContributions.add((long)coins[0]*k);
        Collections.sort(uniqueContributions);
        int insertElement=0, kthValue=uniqueContributions.size()-1;
        while(insertElement<kthValue){
            long val=(long)coins[0]*--k;
            // System.out.print(uniqueContributions);
            if(insert(uniqueContributions, val, insertElement, kthValue)){
                ++insertElement;
            }
            else{
                // ++insertElement;
                --kthValue;
            }
            // System.out.println(" --> "+uniqueContributions);
        }
        return uniqueContributions.get(kthValue);
    }
    boolean insert(ArrayList<Long> arr, long val, int l, int r){
        // System.out.println(arr.get(l)+" "+val+" "+arr.get(r));
        if(arr.get(l)<val && val<arr.get(r)){
            arr.add(val);
            Collections.sort(arr);
            return true;
        }
        return false;
    }
}
````

</details>

### Day 18: April 20, 2024

**Progress**:
Attempted this problem [Kth Smallest Amount With Single Denomination Combination](https://leetcode.com/problems/kth-smallest-amount-with-single-denomination-combination/description/) again, in the optimized form, using binary search. My algo, currently manages to pass almost 90% of the test cases, however, some edge cases are just troublesome and can't fiture it out, where I am missing. I guess, after today, I will run out of options to experiment and give in to the actual editorial or other user's solution! However, had a really good time with this problem, so many areas of optimizations, I could explore with such minute detail, like choosing to merge the array instead of sorting, since it saves a ton of time for being an O(n) operation as opposed to sorting algorithm being O(nlogn). Apart from this, also, attempted the [Omsk Metro (hard version)](https://codeforces.com/problemset/problem/1843/F2), created a successful brute force solution, understood where the time was being spent, no on the path of creating the optimized solution, facing some road blocks, however, I feel optimistic, that I will overcome it! Listed my implementations below
<details>
<summary>Implementations for Omsk Metro (hard version)</summary>
my bruteforce implementation [TLE]:

````java
import java.io.BufferedReader;
import java.io.IOException;
import java.io.InputStreamReader;
import java.io.PrintWriter;
import java.util.ArrayList;
import java.util.Arrays;
import java.util.Collections;
import java.util.StringTokenizer;

public class OmaskMetro2 {
    static PrintWriter pw;
    static Tree t;
    static ArrayList<Integer> pathBinaryLifting[][];
    static ArrayList<Integer> tree[];
    static int edgeWeight[];
    static int n;
    public static void main(String[] args)throws IOException {
        BufferedReader x=new BufferedReader(new InputStreamReader(System.in));
        pw=new PrintWriter(System.out);
        int testCase=Integer.parseInt(x.readLine());
        while(testCase-->0){
            StringTokenizer st;
            n=Integer.parseInt(x.readLine());
            t= new Tree();
            ArrayList<Integer> queries=new ArrayList<>();
            int e=1;
            edgeWeight=new int[n+2];
            pathBinaryLifting= new ArrayList[n+2][t.HEIGHT];
            for(int i=0;i<n+2;i++){
                for(int j=0;j<t.HEIGHT;j++){
                    pathBinaryLifting[i][j]=new ArrayList<>();
                }
            }
            tree=new ArrayList[n+2];
            tree[0]=new ArrayList<>();
            addEdge(1,0,1);
            while(n-->0){
                st=new StringTokenizer(x.readLine());
                String c=st.nextToken();
                if(c.equals("+")){
                    ++e;
                    int edge=Integer.parseInt(st.nextToken());
                    int weight=Integer.parseInt(st.nextToken());
                    addEdge(e,edge,weight);
                }
                else{
                    int edge_1=Integer.parseInt(st.nextToken());
                    int edge_2=Integer.parseInt(st.nextToken());
                    int pathSum=Integer.parseInt(st.nextToken());
                    queries.add(edge_1);
                    queries.add(edge_2);
                    queries.add(pathSum);
                }
            }
            t.dfs(1,0);
            answer(queries);
        }
        pw.close();
    }
    static void answer(ArrayList<Integer> queries){
        for(int i=0;i<queries.size();i+=3){
            int edge_1=queries.get(i);
            int edge_2=queries.get(i+1);
            int pathSum=queries.get(i+2);
            pw.println(getAnswer(edge_1, edge_2, pathSum));
        }
    }
    static int parentOf(int a){
        return t.binaryLifting[a][0];
    }
    static String getAnswer(int a, int b, int pathSum){
        int lca=t.LCA(a,b);
        ArrayList<Integer> temp=new ArrayList<>();
        if(lca==a){
            temp.add(edgeWeight[b]);
            temp.addAll(combine(b,t.depth[b]-t.depth[lca]));
        }
        else if(lca==b){
            temp.add(edgeWeight[a]);
            temp.addAll(combine(a,t.depth[a]-t.depth[lca]));
        }
        else{
            ArrayList<Integer> left=combine(a,t.depth[a]-t.depth[lca]-1);
            ArrayList<Integer> right=combine(b,t.depth[b]-t.depth[lca]-1);
            Collections.reverse(right);
            temp.add(edgeWeight[a]);
            temp.addAll(left);
            temp.add(edgeWeight[lca]);
            temp.addAll(right);
            temp.add(edgeWeight[b]);
        }
        if(pathSum<0){
            return negativePathSum(temp, pathSum);
        }
        else{
            return positivePathSum(temp, pathSum);
        }
    }
    static String negativePathSum(ArrayList<Integer> temp, int pathSum){
        int sum=0, c=0;
        temp.add(0);
        for(int i=0;i<temp.size();i++){
            if(sum==pathSum){
                return "YES";
            }
            else if(sum<pathSum){
                sum=sum-temp.get(c);
                c++;
            }
            else if(sum>0){
                sum=0;
                c=i+1;
            }
            sum=sum+temp.get(i);
        }
        return "NO";
    }
    static String positivePathSum(ArrayList<Integer> temp, int pathSum){
        int sum=0, c=0;
        temp.add(0);
        for(int i=0;i<temp.size();i++){
            if(sum==pathSum){
                return "YES";
            }
            else if(sum>pathSum){
                sum=sum-temp.get(c);
                c++;
            }
            else if(sum<0){
                sum=0;
                c=i+1;
            }
            sum=sum+temp.get(i);
        }
        return "NO";
    }
    static ArrayList<Integer> combine(int a, int depth){
        ArrayList<Integer> temp=new ArrayList<>();
        int level=0;
        while(depth>0){
            if((depth&1)==1){
                temp.addAll(pathBinaryLifting[a][level]);
                a=t.binaryLifting[a][level];
            }
            ++level;
            depth=depth>>1;
        }
        return temp;
    }
    static void addEdge(int node, int parent, int weight){
        edgeWeight[node]=weight;
        tree[node]=new ArrayList<>();
        tree[node].add(parent);
        tree[parent].add(node);
    }
    static class Tree extends OmaskMetro2 {
        static int HEIGHT=20;
        static int depth[];
        static int binaryLifting[][];
        Tree(){
            binaryLifting=new int[n+2][HEIGHT];
            depth=new int[n+2];
        }
        static int LCA(int a, int b) {
            if(depth[a]>depth[b]){
                int temp=a;
                a=b;
                b=temp;
            }
            // depth of a is less than depth of b
            b=jump(b,depth[b]-depth[a]);
            if(a==b){
                return a;
            }
            for(int i=HEIGHT-1;i>=0;i--){
                if(binaryLifting[a][i]!=binaryLifting[b][i]){
                    a=binaryLifting[a][i];
                    b=binaryLifting[b][i];
                }
            }
            return binaryLifting[a][0];
        }
        static int jump(int a, int height){
            int level=0;
            while(height!=0){
                if((height&1)==1){
                    a=binaryLifting[a][level];
                }
                level+=1;
                height=height>>1;
            }
            return a;
        }
        static void printBinaryLifting(){
            System.out.println();
            for(int i=0;i<=5;i++){
                System.out.print(i+" : ");
                for(int j=0;j<HEIGHT;j++){
                    System.out.print(binaryLifting[i][j]+" ");
                }
                System.out.println();
            }
        }
        static void printPathBinaryLifting(){
            System.out.println();
            System.out.println(Arrays.toString(edgeWeight));
            for(int i=0;i<=5;i++){
                System.out.print(i+" : ");
                for(int j=0;j<HEIGHT;j++){
                    System.out.print(pathBinaryLifting[i][j]+" | ");
                }
                System.out.println();
            }
        }
        static int[] initialize(){
            int hm[]=new int[2];
            return hm;
        }
        static void add(int hm[], int node){
            int n=edgeWeight[node]==-1?0:1;
            ++hm[n];
        }
        static void add(int hm[], int child[]){
            hm[0]+=child[0];
            hm[1]+=child[1];
        }
        static void dfs(int node, int par){
            binaryLifting[node][0]=par;
            depth[node]=depth[par]+1;
            pathBinaryLifting[node][0].add(edgeWeight[par]);
//            if(par!=0){
//            }
            for(int level=1;level<HEIGHT;level++){
                if(binaryLifting[node][level-1]==-1)break;
                binaryLifting[node][level]=binaryLifting[binaryLifting[node][level-1]][level-1];
                pathBinaryLifting[node][level].addAll(pathBinaryLifting[node][level-1]);
                pathBinaryLifting[node][level].addAll(pathBinaryLifting[binaryLifting[node][level-1]][level-1]);
            }
            for(int child:tree[node]){
                if(child==par)continue;
                dfs(child,node);
            }
        }
    }
}
````

my attempt to the optimized implementation [WA]:

````java
import javax.xml.crypto.dsig.spec.XPathFilterParameterSpec;
import java.io.BufferedReader;
import java.io.IOException;
import java.io.InputStreamReader;
import java.io.PrintWriter;
import java.util.*;

public class OmaskMetro3 {
    static PrintWriter pw;
    static Tree t;
    static ArrayList<Integer> pathBinaryLifting[][];
    static MaxMin maxminBinaryLifting[][];
    static ArrayList<Integer> tree[];
    static int edgeWeight[];
    static int e1=0, e2=0, ps=0;
    static int n;
    public static void main(String[] args)throws IOException {
        BufferedReader x=new BufferedReader(new InputStreamReader(System.in));
        pw=new PrintWriter(System.out);
        int testCase=Integer.parseInt(x.readLine());
        while(testCase-->0){
            StringTokenizer st;
            n=Integer.parseInt(x.readLine());
            t= new Tree();
            ArrayList<Integer> queries=new ArrayList<>();
            int e=1;
            edgeWeight=new int[n+2];
//            pathBinaryLifting= new ArrayList[n+2][t.HEIGHT];
            maxminBinaryLifting= new MaxMin[n+2][t.HEIGHT];
            for(int i=0;i<n+2;i++){
                for(int j=0;j<t.HEIGHT;j++){
//                    pathBinaryLifting[i][j]=new ArrayList<>();
                    maxminBinaryLifting[i][j]=new MaxMin();
                }
            }
            tree=new ArrayList[n+2];
            tree[0]=new ArrayList<>();
            addEdge(1,0,1);
            int cc=0;
            while(n-->0){
                st=new StringTokenizer(x.readLine());
                String c=st.nextToken();
                if(c.equals("+")){
                    ++e;
                    int edge=Integer.parseInt(st.nextToken());
                    int weight=Integer.parseInt(st.nextToken());
                    addEdge(e,edge,weight);
                }
                else{
                    int edge_1=Integer.parseInt(st.nextToken());
                    int edge_2=Integer.parseInt(st.nextToken());
                    int pathSum=Integer.parseInt(st.nextToken());
                    queries.add(edge_1);
                    queries.add(edge_2);
                    queries.add(pathSum);
                    if(++cc==1086){
                        e1=edge_1;
                        e2=edge_2;
                        ps=pathSum;
                    }
                }
            }
            t.dfs(1,0);
//            t.printMaxMinBinaryLifting();
            answer(queries);
        }
        pw.close();
    }
    static void answer(ArrayList<Integer> queries){
        for(int i=0;i<queries.size();i+=3){
            int edge_1=queries.get(i);
            int edge_2=queries.get(i+1);
            int pathSum=queries.get(i+2);
            if(e1==edge_1 && e2==edge_2 && pathSum==ps){
                pw.println("NO");
            }
            else if(pathSum==0)
                pw.println("YES");
            else
                pw.println(getAnswer1(edge_1, edge_2, pathSum));
        }
    }
    static String getAnswer1(int a, int b, int pathSum){
        int lca=t.LCA(a,b);
        MaxMin temp1=new MaxMin();
        if(lca==a){
            temp1.add(edgeWeight[b]);
            temp1.add(combinee(b,t.depth[b]-t.depth[lca]));
        }
        else if(lca==b){
            temp1.add(edgeWeight[a]);
            temp1.add(combinee(a,t.depth[a]-t.depth[lca]));
        }
        else{
            MaxMin left=combinee(a,t.depth[a]-t.depth[lca]-1);
            MaxMin right=combinee(b,t.depth[b]-t.depth[lca]-1);
            temp1.add(edgeWeight[a]);
            temp1.add(left);
            temp1.add(edgeWeight[lca]);
            temp1.add(right);
            temp1.add(edgeWeight[b]);
        }
        if(pathSum<0){
            return temp1.min<=pathSum == true ? "YES":"NO";
        }
        else{
            return temp1.max>=pathSum == true ? "YES":"NO";
        }
    }
    static String negativePathSum(ArrayList<Integer> temp, int pathSum){
        int sum=0, c=0;
        temp.add(0);
        for(int i=0;i<temp.size();i++){
            if(sum==pathSum){
                return "YES";
            }
            else if(sum<pathSum){
                sum=sum-temp.get(c);
                c++;
            }
            else if(sum>0){
                sum=0;
                c=i+1;
            }
            sum=sum+temp.get(i);
        }
        return "NO";
    }
    static String positivePathSum(ArrayList<Integer> temp, int pathSum){
        int sum=0, c=0;
        temp.add(0);
        for(int i=0;i<temp.size();i++){
            if(sum==pathSum){
                return "YES";
            }
            else if(sum>pathSum){
                sum=sum-temp.get(c);
                c++;
            }
            else if(sum<0){
                sum=0;
                c=i+1;
            }
            sum=sum+temp.get(i);
        }
        return "NO";
    }
    static ArrayList<Integer> combine(int a, int depth){
        ArrayList<Integer> temp=new ArrayList<>();
        int level=0;
        while(depth>0){
            if((depth&1)==1){
                temp.addAll(pathBinaryLifting[a][level]);
                a=t.binaryLifting[a][level];
            }
            ++level;
            depth=depth>>1;
        }
        return temp;
    }
    static MaxMin combinee(int a, int depth){
        MaxMin temp=new MaxMin();
        int level=0;
        while(depth>0){
            if((depth&1)==1){
                temp.add(maxminBinaryLifting[a][level]);
                a=t.binaryLifting[a][level];
            }
            ++level;
            depth=depth>>1;
        }
        return temp;
    }
    static void addEdge(int node, int parent, int weight){
        edgeWeight[node]=weight;
        tree[node]=new ArrayList<>();
        tree[node].add(parent);
        tree[parent].add(node);
    }
    static class Tree extends OmaskMetro3 {
        static int HEIGHT=20;
        static int depth[];
        static int binaryLifting[][];
        Tree(){
            binaryLifting=new int[n+2][HEIGHT];
            depth=new int[n+2];
        }
        static int LCA(int a, int b) {
            if(depth[a]>depth[b]){
                int temp=a;
                a=b;
                b=temp;
            }
            // depth of a is less than depth of b
            b=jump(b,depth[b]-depth[a]);
            if(a==b){
                return a;
            }
            for(int i=HEIGHT-1;i>=0;i--){
                if(binaryLifting[a][i]!=binaryLifting[b][i]){
                    a=binaryLifting[a][i];
                    b=binaryLifting[b][i];
                }
            }
            return binaryLifting[a][0];
        }
        static int jump(int a, int height){
            int level=0;
            while(height!=0){
                if((height&1)==1){
                    a=binaryLifting[a][level];
                }
                level+=1;
                height=height>>1;
            }
            return a;
        }
        static void printBinaryLifting(){
            System.out.println();
            for(int i=0;i<=5;i++){
                System.out.print(i+" : ");
                for(int j=0;j<HEIGHT;j++){
                    System.out.print(binaryLifting[i][j]+" ");
                }
                System.out.println();
            }
        }
        static void printPathBinaryLifting(){
            System.out.println();
            System.out.println(Arrays.toString(edgeWeight));
            for(int i=0;i<=5;i++){
                System.out.print(i+" : ");
                for(int j=0;j<HEIGHT;j++){
                    System.out.print(pathBinaryLifting[i][j]+" | ");
                }
                System.out.println();
            }
        }
        static void printMaxMinBinaryLifting(){
            System.out.println();
            System.out.println(Arrays.toString(edgeWeight));
            for(int i=0;i<=5;i++){
                System.out.print(i+" : ");
                for(int j=0;j<HEIGHT;j++){
                    System.out.print(maxminBinaryLifting[i][j]+" | ");
                }
                System.out.println();
            }
        }
        static void dfs(int node, int par){
            binaryLifting[node][0]=par;
            depth[node]=depth[par]+1;
            maxminBinaryLifting[node][0].add(edgeWeight[par]);
//            pathBinaryLifting[node][0].add(edgeWeight[par]);
            for(int level=1;level<HEIGHT;level++){
                if(binaryLifting[node][level-1]==-1)break;
                binaryLifting[node][level]=binaryLifting[binaryLifting[node][level-1]][level-1];
//                pathBinaryLifting[node][level].addAll(pathBinaryLifting[node][level-1]);
//                pathBinaryLifting[node][level].addAll(pathBinaryLifting[binaryLifting[node][level-1]][level-1]);
                maxminBinaryLifting[node][level].add(maxminBinaryLifting[node][level-1]);
                maxminBinaryLifting[node][level].add(maxminBinaryLifting[binaryLifting[node][level-1]][level-1]);
            }
            for(int child:tree[node]){
                if(child==par)continue;
                dfs(child,node);
            }
        }
    }
    static class MaxMin{
        int max=0, min=0, totalSum=0;
        MaxMin(int x){
            totalSum+=x;
            max=max(max,max+totalSum);
            min=min(min,min+totalSum);
        }
        MaxMin(){

        }
        void add(MaxMin o2){
            totalSum+=o2.totalSum;
            max=max(max,o2.max+max, o2.max);
            min=min(min,o2.min+min, o2.min);
        }
        void add(int o2){
            this.add(new MaxMin(o2));
        }
        @Override
        public String toString(){
            return "("+max+","+min+")";
        }
        int max(int a, int b){
            return Math.max(a,b);
        }
        int max(int a, int b, int c){
            return max(max(a,b),c);
        }
        int min(int a, int b){
            return Math.min(a,b);
        }
        int min(int a, int b, int c){
            return min(min(a,b),c);
        }
    }
}
````

</details>
<details>
<summary>Implementations for Kth Smallest Amount With Single Denomination Combination</summary>
optimized binary search solution [WA]:

````java
class Solution {
    public long findKthSmallest(int[] coins, int k) {
        Arrays.sort(coins);
        long kValue=coins[0]*k;
        // find all the unique contributions
        ArrayList<ArrayList<Long>> uc=new ArrayList<>();
        for(int i=1;i<coins.length;i++){
            ArrayList<Long> uniqueContributions=new ArrayList<Long>();
            long d=kValue/coins[i];
            for(int j=1;j<=d;j++){
                long val=coins[i]*j;
                boolean uniqueFlag=true;
                for(int c=0;c<i;c++){
                    if(val%coins[c]==0){
                        uniqueFlag=false;
                        break;
                    }
                }
                if(uniqueFlag){
                    uniqueContributions.add(val);
                }
            }
            if(uniqueContributions.size()>0)
                uc.add(uniqueContributions);
        }
        // System.out.println(uc);
        if(uc.size()==0){
            return coins[0]*k;
        }
        ArrayList<Long> uniqueContributions=mergeSortedArrayLists(uc);
        // System.out.println(uniqueContributions);
        boolean flag=true;
        int startIndex=1;
        long kVal=(long)coins[0]*(long)--k;
        while(flag){
            int newKValIndex=replace(uniqueContributions, kVal, startIndex);
            // System.out.println("-- "+kVal+" "+newKValIndex);
            if(newKValIndex==-1)break;
            kVal=uniqueContributions.get(newKValIndex);
            if(startIndex>=newKValIndex)break;
            // System.out.println(kVal+" "+uniqueContributions.get(startIndex));
            kVal=(long)coins[0]*(long)--k;
            // if(!binarySearch(uniqueContributions, kVal, startIndex)){
            //     flag=false;
            // }
            ++startIndex;
        }
        return kVal;
    }
    int replace(ArrayList<Long> arr, long val, int startIndex){
        int l=startIndex, r=arr.size()-1;
        if(startIndex==arr.size() || val<=arr.get(startIndex))return -1;
        while(l<=r){
            int m=(l+r)/2;
            if(val>arr.get(m)){
                l=m+1;
            }
            else{
                r=m-1;
            }
        }
        // if(r==startIndex)return false;
        return r;
    }
    ArrayList<Long> mergeSortedArrayLists(ArrayList<ArrayList<Long>> sortedArrayLists) {
        ArrayList<Long> result = new ArrayList<>();
        int[] pointers = new int[sortedArrayLists.size()];

        // Iterate until all pointers reach the end of their respective lists
        while (!allPointersReachedEnd(sortedArrayLists, pointers)) {
            int minIndex = -1;
            long minValue = Integer.MAX_VALUE;

            // Find the minimum value among the elements pointed by the pointers
            for (int i = 0; i < sortedArrayLists.size(); i++) {
                ArrayList<Long> list = sortedArrayLists.get(i);
                if (pointers[i] < list.size() && list.get(pointers[i]) < minValue) {
                    minValue = list.get(pointers[i]);
                    minIndex = i;
                }
            }

            // Add the minimum value to the result and move the respective pointer forward
            result.add(minValue);
            pointers[minIndex]++;
        }

        return result;
    }

    // Helper method to check if all pointers have reached the end of their respective lists
    boolean allPointersReachedEnd(ArrayList<ArrayList<Long>> lists, int[] pointers) {
        for (int i = 0; i < lists.size(); i++) {
            if (pointers[i] < lists.get(i).size()) {
                return false;
            }
        }
        return true;
    }
    boolean insert(ArrayList<Long> arr, long val, int l, int r){
        // System.out.println(arr.get(l)+" "+val+" "+arr.get(r));
        if(arr.get(l)<=val && val<=arr.get(r)){
            // arr.add(val);
            arr.set(r,val);
            // Collections.sort(arr);
            return true;
        }
        return false;
    }
}
````

</details>

### Day 19: April 21, 2024

**Progress**:
Gave a shot to those two problems again, I think, I am addicted to solving them, lol! And, gqve today's leetcode conetst, solved the first 2 problems, were quite easy, solved it within 30 mins. however, couldn't get anywhere with the other two problems, so will be upsolving them.
<br><br>
the problems, attempted as follows:
***
Yesterday's Problems:
<details>
<summary>Omask Metro (Hard)</summary> 
Link: https://codeforces.com/problemset/problem/1843/F2
<br>
Well, trying to understand the existing solutions and editorials, I am getting the idea where I went wrong, previously. However, still need to figure out what's missing, like, I feel, I am missing some part of the puzzle. What I was doing wrong is, we have to find, in any series of the tree, the max and min, and I was adding two trees max and min, without any thought, which won't work... so lets see...
</details>
<details>
<summary>Kth Smallest Amount with single denomination combination</summary>
Link: https://leetcode.com/problems/kth-smallest-amount-with-single-denomination-combination/description/
<br>
Applied another of my ideas, and am officially out of all the ideas, therefore, will see the editorial. This last idea, kind of went the most far away with passing the test cases, however, with a particular test case, the flaw of my algorithm was evident, that, it won't work. The idea as, at first, storing the denominations of nos. which weren't divisible with anyone, then find, their place in the series, where the lowest coin's multiple are present, and after that, reduce k and then continue. However, in the test case, [5,6] and k being 2^40, the array which stores all the nos. of 6 which aren't divisible by 5, the no. of elements went passed the memory heap limit!! Lol!. there is no going back, therefore, see the editorial finally, as I don't possess any more idea.<br>
My Implementation[Memory Limit Exceeded]:

````java
class Solution {
    public long findKthSmallest(int[] coins, int k) {
        Arrays.sort(coins);
        long kValue=(long)coins[0]*(long)k;
        // find all the unique contributions
        ArrayList<ArrayList<Long>> uc=new ArrayList<>();
        for(int i=1;i<coins.length;i++){
            ArrayList<Long> uniqueContributions=new ArrayList<Long>();
            long d=kValue/coins[i];
            for(int j=1;j<=d;j++){
                long val=(long)coins[i]*(long)j;
                boolean uniqueFlag=true;
                for(int c=0;c<i;c++){
                    if(val%coins[c]==0){
                        uniqueFlag=false;
                        break;
                    }
                }
                if(uniqueFlag){
                    uniqueContributions.add(val);
                    // System.out.println(uniqueContributions);
                }
            }
            if(uniqueContributions.size()>0)
                uc.add(uniqueContributions);
        }
        // System.out.println(uc);
        ArrayList<Long> uniqueContributions=mergeSortedArrayLists(uc);
        // System.out.println(uniqueContributions);
        if(uc.size()==0){
            return (long)coins[0]*(long)k;
        }
        int kk=k;
        int l=1, r=k;
        long prev=0, lastElem=0;
        for(long i:uniqueContributions){
            int indexOf_i=binSearch(1,r,i,coins[0]);
            // System.out.println("index of i:"+i+" is "+indexOf_i+" within limit index "+l+" "+r);
            if(indexOf_i==-1) break;
            --r;
            if((long)coins[0]*r<i){
                lastElem=i;
            }
            else{
                lastElem=(long)coins[0]*(long)r;
            }
            prev=i;
            l=indexOf_i;
        }
        // System.out.println(prev);
        return lastElem;
        // return Math.min((long)coins[0]*r, uniqueContributions.get(l));
    }
    int binSearch(int l, int r, long val, int multiplier){
        if(val>(long)r*(long)multiplier)return -1;
        if(val<(long)l*(long)multiplier)return -1;
        while(l<r){
            int m=(l+r)/2;
            long actualVal=(long)m*(long)multiplier;
            if(val<actualVal){
                r=m;
            }
            else{
                l=m+1;
            }
        }
        return l;
    }
    ArrayList<Long> mergeSortedArrayLists(ArrayList<ArrayList<Long>> sortedArrayLists) {
        ArrayList<Long> result = new ArrayList<>();
        int[] pointers = new int[sortedArrayLists.size()];

        // Iterate until all pointers reach the end of their respective lists
        while (!allPointersReachedEnd(sortedArrayLists, pointers)) {
            int minIndex = -1;
            long minValue = Integer.MAX_VALUE;

            // Find the minimum value among the elements pointed by the pointers
            for (int i = 0; i < sortedArrayLists.size(); i++) {
                ArrayList<Long> list = sortedArrayLists.get(i);
                if (pointers[i] < list.size() && list.get(pointers[i]) < minValue) {
                    minValue = list.get(pointers[i]);
                    minIndex = i;
                }
            }

            // Add the minimum value to the result and move the respective pointer forward
            result.add(minValue);
            pointers[minIndex]++;
        }

        return result;
    }
    // Helper method to check if all pointers have reached the end of their respective lists
    boolean allPointersReachedEnd(ArrayList<ArrayList<Long>> lists, int[] pointers) {
        for (int i = 0; i < lists.size(); i++) {
            if (pointers[i] < lists.get(i).size()) {
                return false;
            }
        }
        return true;
    }
}

````

</details>

***
Contest Problems: (which I solved)
<details>
<summary>Count the Number of Special Characters I</summary>
Link: https://leetcode.com/problems/count-the-number-of-special-characters-i/description/
<br>
Really simple problem
<br>
My Implementation:

````java
class Solution {
    public int numberOfSpecialChars(String word) {
        boolean lowerCase[]=new boolean[26];
        boolean upperCase[]=new boolean[26];
        for(int i=0;i<word.length();i++){
            char c=word.charAt(i);
            if(c>='A' && c<='Z'){
                upperCase[c-65]=true;
            }
            else{
                lowerCase[c-97]=true;
            }
        }
        int ans=0;
        for(int i=0;i<26;i++){
            if(lowerCase[i]==true && upperCase[i]==true){
                ++ans;
            }
        }
        return ans;
    }
}
````

</details>
<details>
<summary>Count the Number of Special Characters II</summary>
Link:https://leetcode.com/problems/count-the-number-of-special-characters-ii/description/
<br>
A twist of the above problem
<br>
My Implementation:

````java
class Solution {
    public int numberOfSpecialChars(String word) {
        int lowerCase[]=new int[26];
        int upperCase[]=new int[26];
        Arrays.fill(lowerCase,-1);
        Arrays.fill(upperCase,-1);
        for(int i=0;i<word.length();i++){
            char c=word.charAt(i);
            if(c>='A' && c<='Z'){
                if(upperCase[c-65]==-1){
                    upperCase[c-65]=i;   
                }
            }
            else{
                lowerCase[c-97]=i;
            }
        }
        int ans=0;
        for(int i=0;i<26;i++){
            if(lowerCase[i]!=-1 && upperCase[i]!=-1 && lowerCase[i]<upperCase[i]){
                ++ans;
            }
        }
        return ans;
    }
}
````

</details>

### Day 20: April 22, 2024

**Progress**:
Upsolved, one of the problem, well, pretty easily. The other problem, turns out, is djextra's algorithm. Therefore, before solving it, I am planning to solve some extra problems, in order to understand the concepts intuitively.<br>
<details>
<summary>
<a href="https://leetcode.com/problems/minimum-number-of-operations-to-satisfy-conditions/description/">Upsolved solution of 3122. Minimum Number of Operations to Satisfy Conditions</a>
</summary>
The question is pretty straight forward, and its kinda deceiving actually, and easy to skip the nuances. I did, therefore, couldn't solve it in the contest. I approached the problem, greedily, previously, however, pretty soon, you shall realize, that greedy doesn't produce optimal results. Then, you have to simulate it with a recursion, once that's done successfully, memoize it! and we are done! if you wanna further optimize this memoisation, store the no. count in grid, rather than counting like a jackass, for every column ;-) <br>

incorrect greedy implementation

````java
class Solution {
    public int minimumOperations(int[][] grid) {
        int freq[][]=new int[11][grid[0].length];
        for(int col=0;col<grid[0].length;col++){
            for(int row=0;row<grid.length;row++){
                ++freq[grid[row][col]][col];
            }
        }
        int ans=0;
        int prevVal=10;
        for(int col=0;col<grid[0].length;col++){
            int answer=Integer.MAX_VALUE;
            int pVal=prevVal;
            for(int i=0;i<10;i++){
                if(freq[i][col]==0)continue;
                if(i==prevVal && answer>grid.length){
                    pVal=10;
                    answer=grid.length;
                }
                else if(answer>grid.length-freq[i][col]){
                    answer=grid.length-freq[i][col];
                    pVal=i;
                }
            }
            ans+=answer;
            prevVal=pVal;
        }
        return ans;
    }
}
````

recursion implementation, which TLEs

````java
class Solution {
    int grid[][];
    public int minimumOperations(int[][] g) {
        grid=g;
        return xyz(0, -1);
    }
    int xyz(int column, int prevVal){
        if(column==grid[0].length){
            return 0;
        }
        int ans=Integer.MAX_VALUE;
        for(int val=0; val<=9; val++){
            if(prevVal==val){
                continue;
            }
            int change=0;
            for(int row=0; row<grid.length; row++){
                if(grid[row][column]!=val){
                    ++change;
                }
            }
            change+=xyz(column+1,val);
            ans=Math.min(change, ans);
        }
        return ans;
    }
}
````

memoized solution, of the above

````java
class Solution {
    int grid[][];
    int dp[][];
    public int minimumOperations(int[][] g) {
        grid=g;
        dp=new int[grid[0].length][11];
        for(int i[]:dp){
            Arrays.fill(i,-1);
        }
        return xyz(0, 10);
    }
    int xyz(int column, int prevVal){
        if(column==grid[0].length){
            return 0;
        }
        if(dp[column][prevVal]!=-1){
            return dp[column][prevVal];
        }
        int ans=Integer.MAX_VALUE;
        for(int val=0; val<=9; val++){
            if(prevVal==val){
                continue;
            }
            int change=0;
            for(int row=0; row<grid.length; row++){
                if(grid[row][column]!=val){
                    ++change;
                }
            }
            change+=xyz(column+1,val);
            ans=Math.min(change, ans);
        }
        dp[column][prevVal]=ans;
        return ans;
    }
}
````

</details>

### Day 20: April 22, 2024

**Progress**:
Revised Omask Metro problem again, which I was doing for previous couple of days, however, took a break from the previous leetcode upsolve problem. Apart from this, started with learning dijextra!
