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

**Thoughts**: Like the previous problems, listed above, this is also a variation of binary jumping problem. The problem was, to find the lowest 10 people, between a and b node. So implementing the binary jumping algorithm, by processing and storing the result, and then answering the query. 

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
