
// CheatSheetCodes.java
// Compact exam-style codes adapted to your class style.

import java.util.*;

class GreedyAndOtherCodes {

    // Master Theorem has no code; it is a formula.

    // Gale-Shapley
    static int[] stableMatching(int[][] men, int[][] women){
        int n = men.length;
        int[] wp = new int[n], mp = new int[n], next = new int[n];
        Arrays.fill(wp, -1);
        Arrays.fill(mp, -1);
        int[][] rank = new int[n][n];
        for(int w=0; w<n; w++) for(int i=0; i<n; i++) rank[w][women[w][i]] = i;
        Queue<Integer> q = new LinkedList<>();
        for(int m=0; m<n; m++) q.add(m);

        while(!q.isEmpty()){
            int m = q.poll();
            int w = men[m][next[m]++];
            if(wp[w] == -1){
                wp[w] = m; mp[m] = w;
            }else{
                int old = wp[w];
                if(rank[w][m] < rank[w][old]){
                    wp[w] = m; mp[m] = w; mp[old] = -1; q.add(old);
                }else q.add(m);
            }
        }
        return mp; // mp[m] = matched woman
    }

    // Interval Scheduling [start,end]
    static int intervalScheduling(int[][] a){
        Arrays.sort(a, (x,y) -> x[1] - y[1]);
        int cnt = 0, last = Integer.MIN_VALUE;
        for(int[] x : a) if(x[0] >= last){ cnt++; last = x[1]; }
        return cnt;
    }

    // Interval Partitioning [start,end]
    static int intervalPartitioning(int[][] a){
        Arrays.sort(a, (x,y) -> x[0] - y[0]);
        PriorityQueue<Integer> pq = new PriorityQueue<>();
        for(int[] x : a){
            if(!pq.isEmpty() && pq.peek() <= x[0]) pq.poll();
            pq.add(x[1]);
        }
        return pq.size();
    }

    // Min Lateness [processing,deadline]
    static int minLateness(int[][] jobs){
        Arrays.sort(jobs, (a,b) -> a[1] - b[1]);
        int t = 0, ans = 0;
        for(int[] j : jobs){
            t += j[0];
            ans = Math.max(ans, Math.max(0, t - j[1]));
        }
        return ans;
    }

    // Optimal Caching: returns fault count
    static int optimalCaching(int[] req, int k){
        HashSet<Integer> cache = new HashSet<>();
        int faults = 0;
        for(int i=0; i<req.length; i++){
            if(cache.contains(req[i])) continue;
            if(cache.size() < k) cache.add(req[i]);
            else{
                int rem = -1, far = -1;
                for(int x : cache){
                    int j = i + 1;
                    while(j < req.length && req[j] != x) j++;
                    if(j == req.length){ rem = x; break; }
                    if(j > far){ far = j; rem = x; }
                }
                cache.remove(rem);
                cache.add(req[i]);
            }
            faults++;
        }
        return faults;
    }

    // Huffman
    static class HNode{
        int f; char c; HNode l, r;
        HNode(int f, char c){ this.f = f; this.c = c; }
        HNode(int f, HNode l, HNode r){ this.f = f; this.l = l; this.r = r; }
    }

    static HNode huffman(char[] ch, int[] freq){
        PriorityQueue<HNode> pq = new PriorityQueue<>((a,b) -> a.f - b.f);
        for(int i=0; i<ch.length; i++) pq.add(new HNode(freq[i], ch[i]));
        while(pq.size() > 1){
            HNode x = pq.poll(), y = pq.poll();
            pq.add(new HNode(x.f + y.f, x, y));
        }
        return pq.poll();
    }

    static void printCodes(HNode r, String s){
        if(r == null) return;
        if(r.l == null && r.r == null){
            System.out.println(r.c + " : " + s);
            return;
        }
        printCodes(r.l, s + "0");
        printCodes(r.r, s + "1");
    }
}


/* ===================== ARRAY.GRAPH.GRAPH METHODS =====================

Paste these INSIDE your Array.Graph.Graph class.
They match your adjacency-matrix style and vertexCount/edges fields.

public boolean hasCycle(int v, boolean[] visited){
    return hasCycleDFS(v, visited, new boolean[vertexCount]);
}
private boolean hasCycleDFS(int v, boolean[] visited, boolean[] inStack){
    visited[v] = true;
    inStack[v] = true;
    for(int i=0; i<vertexCount; i++){
        if(edges[v][i] > 0){
            if(!visited[i] && hasCycleDFS(i, visited, inStack)) return true;
            if(inStack[i]) return true;
        }
    }
    inStack[v] = false;
    return false;
}

public boolean isDAG(){
    boolean[] visited = new boolean[vertexCount];
    for(int i=0; i<vertexCount; i++)
        if(!visited[i] && hasCycle(i, visited)) return false;
    return true;
}

public boolean isStronglyConnected(){
    boolean[] vis = new boolean[vertexCount];
    vis[0] = true;
    depthFirstSearch(vis, 0);
    for(boolean b : vis) if(!b) return false;

    Graph g2 = new Graph(vertexCount);
    for(int i=0; i<vertexCount; i++)
        for(int j=0; j<vertexCount; j++)
            if(edges[i][j] > 0) g2.addEdge(j, i, edges[i][j]);

    vis = new boolean[vertexCount];
    vis[0] = true;
    g2.depthFirstSearch(vis, 0);
    for(boolean b : vis) if(!b) return false;
    return true;
}

public int[] bfsLengths(int start){
    int[] dist = new int[vertexCount];
    for(int i=0; i<vertexCount; i++) dist[i] = -1;
    boolean[] vis = new boolean[vertexCount];
    Array.Queue q = new Array.Queue(vertexCount + 5);
    q.enqueue(new Array.Element(start));
    vis[start] = true;
    dist[start] = 0;
    while(!q.isEmpty()){
        int u = q.dequeue().getData();
        for(int v=0; v<vertexCount; v++){
            if(edges[u][v] > 0 && !vis[v]){
                vis[v] = true;
                dist[v] = dist[u] + 1;
                q.enqueue(new Array.Element(v));
            }
        }
    }
    return dist;
}

public int[] dijkstra(int start){
    int[] d = new int[vertexCount];
    boolean[] vis = new boolean[vertexCount];
    for(int i=0; i<vertexCount; i++) d[i] = Integer.MAX_VALUE / 4;
    d[start] = 0;
    for(int k=0; k<vertexCount; k++){
        int u = -1;
        for(int i=0; i<vertexCount; i++)
            if(!vis[i] && (u == -1 || d[i] < d[u])) u = i;
        if(u == -1) break;
        vis[u] = true;
        for(int v=0; v<vertexCount; v++)
            if(edges[u][v] > 0 && d[u] + edges[u][v] < d[v])
                d[v] = d[u] + edges[u][v];
    }
    return d;
}

public int primMST(){
    int[] key = new int[vertexCount];
    boolean[] in = new boolean[vertexCount];
    for(int i=0; i<vertexCount; i++) key[i] = Integer.MAX_VALUE / 4;
    key[0] = 0;
    int sum = 0;
    for(int k=0; k<vertexCount; k++){
        int u = -1;
        for(int i=0; i<vertexCount; i++)
            if(!in[i] && (u == -1 || key[i] < key[u])) u = i;
        in[u] = true;
        sum += key[u];
        for(int v=0; v<vertexCount; v++)
            if(edges[u][v] > 0 && !in[v] && edges[u][v] < key[v])
                key[v] = edges[u][v];
    }
    return sum;
}

public int kruskalMST(){
    int m = 0;
    for(int i=0; i<vertexCount; i++)
        for(int j=i+1; j<vertexCount; j++)
            if(edges[i][j] > 0 || edges[j][i] > 0) m++;

    int[][] e = new int[m][3];
    int idx = 0;
    for(int i=0; i<vertexCount; i++){
        for(int j=i+1; j<vertexCount; j++){
            int w = 0;
            if(edges[i][j] > 0) w = edges[i][j];
            else if(edges[j][i] > 0) w = edges[j][i];
            if(w > 0){
                e[idx][0] = i; e[idx][1] = j; e[idx][2] = w; idx++;
            }
        }
    }

    java.util.Arrays.sort(e, (a,b) -> a[2] - b[2]);
    Array.DisjointSet ds = new Array.DisjointSet(vertexCount);
    int sum = 0, taken = 0;
    for(int i=0; i<e.length && taken < vertexCount - 1; i++){
        int u = e[i][0], v = e[i][1];
        if(ds.findSetIterative(u) != ds.findSetIterative(v)){
            ds.union(u, v);
            sum += e[i][2];
            taken++;
        }
    }
    return sum;
}

public boolean isBipartiteMatrix(){
    int[] color = new int[vertexCount];
    for(int i=0; i<vertexCount; i++) color[i] = -1;
    Array.Queue q = new Array.Queue(vertexCount + 5);

    for(int s=0; s<vertexCount; s++){
        if(color[s] != -1) continue;
        color[s] = 0;
        q.enqueue(new Array.Element(s));

        while(!q.isEmpty()){
            int u = q.dequeue().getData();
            for(int v=0; v<vertexCount; v++){
                if(edges[u][v] > 0 || edges[v][u] > 0){
                    if(color[v] == -1){
                        color[v] = 1 - color[u];
                        q.enqueue(new Array.Element(v));
                    }else if(color[v] == color[u]) return false;
                }
            }
        }
    }
    return true;
}

public int reverseDeleteMST(){
    int m = 0;
    for(int i=0; i<vertexCount; i++)
        for(int j=i+1; j<vertexCount; j++)
            if(edges[i][j] > 0 || edges[j][i] > 0) m++;

    int[][] e = new int[m][3];
    int idx = 0;
    for(int i=0; i<vertexCount; i++){
        for(int j=i+1; j<vertexCount; j++){
            int w = 0;
            if(edges[i][j] > 0) w = edges[i][j];
            else if(edges[j][i] > 0) w = edges[j][i];
            if(w > 0){
                e[idx][0] = i; e[idx][1] = j; e[idx][2] = w; idx++;
            }
        }
    }

    java.util.Arrays.sort(e, (a,b) -> b[2] - a[2]);
    Graph temp = new Graph(vertexCount);
    for(int i=0; i<e.length; i++){
        temp.addEdge(e[i][0], e[i][1], e[i][2]);
        temp.addEdge(e[i][1], e[i][0], e[i][2]);
    }

    for(int i=0; i<e.length; i++){
        int u = e[i][0], v = e[i][1], w = e[i][2];
        temp.edges[u][v] = 0; temp.edges[v][u] = 0;
        boolean[] vis = new boolean[vertexCount];
        vis[0] = true;
        temp.depthFirstSearch(vis, 0);
        boolean ok = true;
        for(boolean b : vis) if(!b) ok = false;
        if(!ok){
            temp.edges[u][v] = w;
            temp.edges[v][u] = w;
        }
    }

    int sum = 0;
    for(int i=0; i<vertexCount; i++)
        for(int j=i+1; j<vertexCount; j++)
            if(temp.edges[i][j] > 0) sum += temp.edges[i][j];
    return sum;
}

public int[] clusterLabels(int k){
    int m = 0;
    for(int i=0; i<vertexCount; i++)
        for(int j=i+1; j<vertexCount; j++)
            if(edges[i][j] > 0 || edges[j][i] > 0) m++;

    int[][] e = new int[m][3];
    int idx = 0;
    for(int i=0; i<vertexCount; i++){
        for(int j=i+1; j<vertexCount; j++){
            int w = 0;
            if(edges[i][j] > 0) w = edges[i][j];
            else if(edges[j][i] > 0) w = edges[j][i];
            if(w > 0){
                e[idx][0] = i; e[idx][1] = j; e[idx][2] = w; idx++;
            }
        }
    }

    java.util.Arrays.sort(e, (a,b) -> a[2] - b[2]);
    Array.DisjointSet ds = new Array.DisjointSet(vertexCount);
    int comps = vertexCount;
    for(int i=0; i<e.length && comps > k; i++){
        int u = e[i][0], v = e[i][1];
        if(ds.findSetIterative(u) != ds.findSetIterative(v)){
            ds.union(u, v);
            comps--;
        }
    }

    int[] lab = new int[vertexCount];
    for(int i=0; i<vertexCount; i++) lab[i] = ds.findSetIterative(i);
    return lab;
}

public int bottleneckOfMST(){
    int m = 0;
    for(int i=0; i<vertexCount; i++)
        for(int j=i+1; j<vertexCount; j++)
            if(edges[i][j] > 0 || edges[j][i] > 0) m++;

    int[][] e = new int[m][3];
    int idx = 0;
    for(int i=0; i<vertexCount; i++){
        for(int j=i+1; j<vertexCount; j++){
            int w = 0;
            if(edges[i][j] > 0) w = edges[i][j];
            else if(edges[j][i] > 0) w = edges[j][i];
            if(w > 0){
                e[idx][0] = i; e[idx][1] = j; e[idx][2] = w; idx++;
            }
        }
    }
    java.util.Arrays.sort(e, (a,b) -> a[2] - b[2]);
    Array.DisjointSet ds = new Array.DisjointSet(vertexCount);
    int mx = 0, taken = 0;
    for(int i=0; i<e.length && taken < vertexCount - 1; i++){
        int u = e[i][0], v = e[i][1];
        if(ds.findSetIterative(u) != ds.findSetIterative(v)){
            ds.union(u, v);
            mx = Math.max(mx, e[i][2]);
            taken++;
        }
    }
    return mx;
}

// cutProperty / cycleProperty / redRule / blueRule are theorem-level ideas.
// In code, Prim/Kruskal implement cut property safely.
// ReverseDelete implements cycle-property intuition (delete heaviest cycle edge).

===================================================================== */


/* ===================== LIST.GRAPH.GRAPH METHODS =====================

Paste these INSIDE your List.Graph.Graph class.

public int bidirectionalEdges(){
    int c = 0;
    for(int i=0; i<vertexCount; i++){
        Edge e = edges[i].getHead();
        while(e != null){
            if(e.getTo() > i && edges[e.getTo()].search(i) != null) c++;
            e = e.getNext();
        }
    }
    return c;
}

public boolean breadthFirstSearch(boolean[] visit, int index1, int index2){
    List.Queue q = new List.Queue();
    q.enqueue(new List.Node(index1));
    visit[index1] = true;
    while(!q.isEmpty()){
        int u = q.dequeue().getData();
        if(u == index2) return true;
        Edge e = edges[u].getHead();
        while(e != null){
            int v = e.getTo();
            if(!visit[v]){
                visit[v] = true;
                q.enqueue(new List.Node(v));
            }
            e = e.getNext();
        }
    }
    return false;
}

public boolean isFullyConnected(){
    boolean[] vis = new boolean[vertexCount];
    vis[0] = true;
    depthFirstSearch(vis, 0);
    for(boolean b : vis) if(!b) return false;
    return true;
}

public boolean isBipartite(){
    int[] color = new int[vertexCount];
    for(int i=0; i<vertexCount; i++) color[i] = -1;
    List.Queue q = new List.Queue();

    for(int s=0; s<vertexCount; s++){
        if(color[s] != -1) continue;
        color[s] = 0;
        q.enqueue(new List.Node(s));

        while(!q.isEmpty()){
            int u = q.dequeue().getData();
            Edge e = edges[u].getHead();
            while(e != null){
                int v = e.getTo();
                if(color[v] == -1){
                    color[v] = 1 - color[u];
                    q.enqueue(new List.Node(v));
                }else if(color[v] == color[u]) return false;
                e = e.getNext();
            }
        }
    }
    return true;
}

public void shortest(int[] lengths, int start){
    boolean[] vis = new boolean[vertexCount];
    List.Queue q = new List.Queue();
    q.enqueue(new List.Node(start));
    vis[start] = true;
    lengths[start] = 0;

    while(!q.isEmpty()){
        int u = q.dequeue().getData();
        Edge e = edges[u].getHead();
        while(e != null){
            int v = e.getTo();
            if(!vis[v]){
                vis[v] = true;
                lengths[v] = lengths[u] + 1;
                q.enqueue(new List.Node(v));
            }
            e = e.getNext();
        }
    }
}

public int shortestIn2Hops(int index1, int index2){
    int ans = Integer.MAX_VALUE / 4;
    Edge e1 = edges[index1].getHead();
    while(e1 != null){
        int mid = e1.getTo();
        Edge e2 = edges[mid].getHead();
        while(e2 != null){
            if(e2.getTo() == index2)
                ans = Math.min(ans, e1.getWeight() + e2.getWeight());
            e2 = e2.getNext();
        }
        e1 = e1.getNext();
    }
    return ans == Integer.MAX_VALUE / 4 ? -1 : ans;
}

public int[] twoHops(int index){
    int cnt = 0;
    Edge e1 = edges[index].getHead();
    while(e1 != null){
        Edge e2 = edges[e1.getTo()].getHead();
        while(e2 != null){ cnt++; e2 = e2.getNext(); }
        e1 = e1.getNext();
    }

    int[] a = new int[cnt];
    int k = 0;
    e1 = edges[index].getHead();
    while(e1 != null){
        Edge e2 = edges[e1.getTo()].getHead();
        while(e2 != null){
            a[k++] = e2.getTo();
            e2 = e2.getNext();
        }
        e1 = e1.getNext();
    }
    return a;
}

===================================================================== */
