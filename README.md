Master: n^d=log_ba<d;n^dlogn=log_ba=d; n^log_ba=log_ba >d; aT(n/b)+O(n^d)
Graphs=BFS=in all possible directions, Bipartite=red or blue no odd length cycle
Strong Connectivity=run bfs in G and G’
DAG=no directed cycles
StableMatching=Gale-shape=best for men
Worst for woman sırayla menler yerleşir n^2
İntervalScheduling=earliest finish time
IntervalPartioning=Early start time #ofclass
Schedule Min Lateness=Earliest Deadline
Optimal Caching=Farthest in future
Shortest Path = dijkstra
MST= 1)Prims= start,add always cheapest
2)Kruskal=sort edges take lowest w no cycle
3)Revere-Del=start all edges del high w, connected olmalı! cutProperty= subset after cut, min cost edge must be in MST
cycleproperty=cycleın maxı mst de yotur
RedRule=uncolored max edge cycleda red
BlueRule=bluesuz Cutsette min seç blue yap
MinBottleneck=find spanning that min most expensive edge=spanningin maxı
Cluster=divide k non empty group add edge to closest ones until remain k clusters! Mst bul k-1 tane en büyük edge sil!
Huffman: en düşük 2 freq al botttom up tree yap sollar 0 sağlar 1!!

class Greedy{
static int[] stableMatching(int[][] men,int[][] women){
int n=men.length;int[] wp=new int[n],mp=new int[n],next=new int[n];
java.util.Arrays.fill(wp,-1);java.util.Arrays.fill(mp,-1);
int[][] rank=new int[n][n];
for(int w=0;w<n;w++)for(int i=0;i<n;i++)rank[w][women[w][i]]=i;
java.util.Queue<Integer> q=new java.util.LinkedList<>();
for(int m=0;m<n;m++)q.add(m);
while(!q.isEmpty()){
int m=q.poll(),w=men[m][next[m]++];
if(wp[w]==-1){wp[w]=m;mp[m]=w;}
else{
int old=wp[w];
if(rank[w][m]<rank[w][old]){
wp[w]=m;mp[m]=w;mp[old]=-1;q.add(old);
}else q.add(m);
}}
return mp;
}
static int intervalScheduling(int[][] a){
java.util.Arrays.sort(a,(x,y)->x[1]-y[1]);
int cnt=0,last=Integer.MIN_VALUE;
for(int[] x:a)if(x[0]>=last){cnt++;last=x[1];}
return cnt;
}
static int intervalPartitioning(int[][] a){
java.util.Arrays.sort(a,(x,y)->x[0]-y[0]);
java.util.PriorityQueue<Integer> pq=new java.util.PriorityQueue<>();
for(int[] x:a){
if(!pq.isEmpty()&&pq.peek()<=x[0])pq.poll();
pq.add(x[1]);
}
return pq.size();
}
static int minLateness(int[][] jobs){
java.util.Arrays.sort(jobs,(a,b)->a[1]-b[1]);
int t=0,ans=0;
for(int[] j:jobs){t+=j[0];ans=Math.max(ans,Math.max(0,t-j[1]));}
return ans;
}
static int optimalCaching(int[] req,int k){
java.util.HashSet<Integer> cache=new java.util.HashSet<>();
int faults=0;
for(int i=0;i<req.length;i++){
if(cache.contains(req[i]))continue;
if(cache.size()<k)cache.add(req[i]);
else{
int rem=-1,far=-1;
for(int x:cache){
int j=i+1;
while(j<req.length&&req[j]!=x)j++;
if(j==req.length){rem=x;break;}
if(j>far){far=j;rem=x;}
}
cache.remove(rem);cache.add(req[i]);
}
faults++;
}
return faults;
}
static class HNode{int f;char c;HNode l,r;
HNode(int f,char c){this.f=f;this.c=c;}
HNode(int f,HNode l,HNode r){this.f=f;this.l=l;this.r=r;}
}
static HNode huffman(char[] ch,int[] freq){
java.util.PriorityQueue<HNode> pq=new java.util.PriorityQueue<>((a,b)->a.f-b.f);
for(int i=0;i<ch.length;i++)pq.add(new HNode(freq[i],ch[i]));
while(pq.size()>1){
HNode x=pq.poll(),y=pq.poll();
pq.add(new HNode(x.f+y.f,x,y));
}
return pq.poll();
}
static void printCodes(HNode r,String s){
if(r==null)return;
if(r.l==null&&r.r==null){System.out.println(r.c+" : "+s);return;}
printCodes(r.l,s+"0");
printCodes(r.r,s+"1");
}
}

public int[] bfs(int s){
int[] d=new int[vertexCount];
boolean[] v=new boolean[vertexCount];
for(int i=0;i<vertexCount;i++)d[i]=-1;
Array.Queue q=new Array.Queue(vertexCount+5);
q.enqueue(new Array.Element(s));v[s]=true;d[s]=0;
while(!q.isEmpty()){
int u=q.dequeue().getData();
for(int i=0;i<vertexCount;i++)
if(edges[u][i]>0&&!v[i]){
v[i]=true;d[i]=d[u]+1;
q.enqueue(new Array.Element(i));
}}
return d;
}
public boolean isBipartite(){
int[] c=new int[vertexCount];
for(int i=0;i<vertexCount;i++)c[i]=-1;
Array.Queue q=new Array.Queue(vertexCount+5);
for(int s=0;s<vertexCount;s++){
if(c[s]!=-1)continue;
c[s]=0;q.enqueue(new Array.Element(s));
while(!q.isEmpty()){
int u=q.dequeue().getData();
for(int v=0;v<vertexCount;v++){
if(edges[u][v]>0||edges[v][u]>0){
if(c[v]==-1){c[v]=1-c[u];q.enqueue(new Array.Element(v));}
else if(c[v]==c[u])return false;
}}}}
return true;
}
public boolean isDAG(){
boolean[] v=new boolean[vertexCount],st=new boolean[vertexCount];
for(int i=0;i<vertexCount;i++)if(!v[i]&&dfsCycle(i,v,st))return false;
return true;
}
private boolean dfsCycle(int u,boolean[] v,boolean[] st){
v[u]=true;st[u]=true;
for(int i=0;i<vertexCount;i++){
if(edges[u][i]>0){
if(!v[i]&&dfsCycle(i,v,st))return true;
if(st[i])return true;
}}
st[u]=false;return false;
}
public boolean isStronglyConnected(){
boolean[] v=new boolean[vertexCount];
v[0]=true;depthFirstSearch(v,0);
for(boolean b:v)if(!b)return false;
Graph g2=new Graph(vertexCount);
for(int i=0;i<vertexCount;i++)
for(int j=0;j<vertexCount;j++)
if(edges[i][j]>0)g2.addEdge(j,i,edges[i][j]);
v=new boolean[vertexCount];v[0]=true;
g2.depthFirstSearch(v,0);
for(boolean b:v)if(!b)return false;
return true;
}
public int[] dijkstra(int s){
int[] d=new int[vertexCount];
boolean[] v=new boolean[vertexCount];
for(int i=0;i<vertexCount;i++)d[i]=1<<30;
d[s]=0;
for(int k=0;k<vertexCount;k++){
int u=-1;
for(int i=0;i<vertexCount;i++)if(!v[i]&&(u==-1||d[i]<d[u]))u=i;
if(u==-1)break;
v[u]=true;
for(int i=0;i<vertexCount;i++)
if(edges[u][i]>0&&d[u]+edges[u][i]<d[i])d[i]=d[u]+edges[u][i];
}
return d;
}
public int prim(){
int[] key=new int[vertexCount];
boolean[] in=new boolean[vertexCount];
for(int i=0;i<vertexCount;i++)key[i]=1<<30;
key[0]=0;int sum=0;
for(int k=0;k<vertexCount;k++){
int u=-1;
for(int i=0;i<vertexCount;i++)if(!in[i]&&(u==-1||key[i]<key[u]))u=i;
if(u==-1)break;
in[u]=true;sum+=key[u];
for(int i=0;i<vertexCount;i++)
if(edges[u][i]>0&&!in[i]&&edges[u][i]<key[i])key[i]=edges[u][i];
}
return sum;
}
public int kruskal(){
int m=0;
for(int i=0;i<vertexCount;i++)
for(int j=i+1;j<vertexCount;j++)
if(edges[i][j]>0||edges[j][i]>0)m++;
int[][] e=new int[m][3];int idx=0;
for(int i=0;i<vertexCount;i++)
for(int j=i+1;j<vertexCount;j++){
int w=edges[i][j]>0?edges[i][j]:edges[j][i];
if(w>0){e[idx][0]=i;e[idx][1]=j;e[idx++][2]=w;}
}
java.util.Arrays.sort(e,(a,b)->a[2]-b[2]);
Array.DisjointSet ds=new Array.DisjointSet(vertexCount);
int sum=0,taken=0;
for(int[] x:e)
if(ds.findSetIterative(x[0])!=ds.findSetIterative(x[1])){
ds.union(x[0],x[1]);sum+=x[2];taken++;
if(taken==vertexCount-1)break;
}
return sum;
}
public int reverseDelete(){
int m=0;
for(int i=0;i<vertexCount;i++)
for(int j=i+1;j<vertexCount;j++)
if(edges[i][j]>0||edges[j][i]>0)m++;
int[][] e=new int[m][3];int idx=0;
for(int i=0;i<vertexCount;i++)
for(int j=i+1;j<vertexCount;j++){
int w=edges[i][j]>0?edges[i][j]:edges[j][i];
if(w>0){e[idx][0]=i;e[idx][1]=j;e[idx++][2]=w;}
}
java.util.Arrays.sort(e,(a,b)->b[2]-a[2]);
Graph temp=new Graph(vertexCount);
for(int[] x:e){
temp.addEdge(x[0],x[1],x[2]);
temp.addEdge(x[1],x[0],x[2]);
}
for(int[] x:e){
int u=x[0],v=x[1],w=x[2];
temp.edges[u][v]=0;temp.edges[v][u]=0;
boolean[] vis=new boolean[vertexCount];
vis[0]=true;temp.depthFirstSearch(vis,0);
boolean ok=true;
for(boolean b:vis)if(!b)ok=false;
if(!ok){temp.edges[u][v]=w;temp.edges[v][u]=w;}
}
int sum=0;
for(int i=0;i<vertexCount;i++)
for(int j=i+1;j<vertexCount;j++)
if(temp.edges[i][j]>0)sum+=temp.edges[i][j];
return sum;
}
public int bottleneck(){
int m=0;
for(int i=0;i<vertexCount;i++)
for(int j=i+1;j<vertexCount;j++)
if(edges[i][j]>0||edges[j][i]>0)m++;
int[][] e=new int[m][3];int idx=0;
for(int i=0;i<vertexCount;i++)
for(int j=i+1;j<vertexCount;j++){
int w=edges[i][j]>0?edges[i][j]:edges[j][i];
if(w>0){e[idx][0]=i;e[idx][1]=j;e[idx++][2]=w;}
}
java.util.Arrays.sort(e,(a,b)->a[2]-b[2]);
Array.DisjointSet ds=new Array.DisjointSet(vertexCount);
int mx=0,taken=0;
for(int[] x:e)
if(ds.findSetIterative(x[0])!=ds.findSetIterative(x[1])){
ds.union(x[0],x[1]);mx=Math.max(mx,x[2]);taken++;
if(taken==vertexCount-1)break;
}
return mx;
}
public int[] cluster(int k){
int m=0;
for(int i=0;i<vertexCount;i++)
for(int j=i+1;j<vertexCount;j++)
if(edges[i][j]>0||edges[j][i]>0)m++;
int[][] e=new int[m][3];int idx=0;
for(int i=0;i<vertexCount;i++)
for(int j=i+1;j<vertexCount;j++){
int w=edges[i][j]>0?edges[i][j]:edges[j][i];
if(w>0){e[idx][0]=i;e[idx][1]=j;e[idx++][2]=w;}
}
java.util.Arrays.sort(e,(a,b)->a[2]-b[2]);
Array.DisjointSet ds=new Array.DisjointSet(vertexCount);
int comp=vertexCount;
for(int[] x:e){
if(comp<=k)break;
if(ds.findSetIterative(x[0])!=ds.findSetIterative(x[1])){
ds.union(x[0],x[1]);comp--;
}}
int[] lab=new int[vertexCount];
for(int i=0;i<vertexCount;i++)lab[i]=ds.findSetIterative(i);
return lab;
}

public int bidirectionalEdges(){
int c=0;
for(int i=0;i<vertexCount;i++){
Edge e=edges[i].getHead();
while(e!=null){
if(e.getTo()>i&&edges[e.getTo()].search(i)!=null)c++;
e=e.getNext();
}}
return c;
}
public boolean breadthFirstSearch(boolean[] visit,int index1,int index2){
List.Queue q=new List.Queue();
q.enqueue(new List.Node(index1));
visit[index1]=true;
while(!q.isEmpty()){
int u=q.dequeue().getData();
if(u==index2)return true;
Edge e=edges[u].getHead();
while(e!=null){
int v=e.getTo();
if(!visit[v]){
visit[v]=true;
q.enqueue(new List.Node(v));
}
e=e.getNext();
}}
return false;
}
public boolean isFullyConnected(){
boolean[] vis=new boolean[vertexCount];
vis[0]=true;
depthFirstSearch(vis,0);
for(boolean b:vis)if(!b)return false;
return true;
}
public boolean isBipartite(){
int[] color=new int[vertexCount];
for(int i=0;i<vertexCount;i++)color[i]=-1;
List.Queue q=new List.Queue();
for(int s=0;s<vertexCount;s++){
if(color[s]!=-1)continue;
color[s]=0;
q.enqueue(new List.Node(s));
while(!q.isEmpty()){
int u=q.dequeue().getData();
Edge e=edges[u].getHead();
while(e!=null){
int v=e.getTo();
if(color[v]==-1){
color[v]=1-color[u];
q.enqueue(new List.Node(v));
}else if(color[v]==color[u])return false;
e=e.getNext();
}}}
return true;
}
public void shortest(int[] lengths,int start){
boolean[] vis=new boolean[vertexCount];
for(int i=0;i<vertexCount;i++)lengths[i]=-1;
List.Queue q=new List.Queue();
q.enqueue(new List.Node(start));
vis[start]=true;
lengths[start]=0;
while(!q.isEmpty()){
int u=q.dequeue().getData();
Edge e=edges[u].getHead();
while(e!=null){
int v=e.getTo();
if(!vis[v]){
vis[v]=true;
lengths[v]=lengths[u]+1;
q.enqueue(new List.Node(v));
}
e=e.getNext();
}}
}
public int shortestIn2Hops(int index1,int index2){
int ans=Integer.MAX_VALUE/4;
Edge e1=edges[index1].getHead();
while(e1!=null){
int mid=e1.getTo();
Edge e2=edges[mid].getHead();
while(e2!=null){
if(e2.getTo()==index2)ans=Math.min(ans,e1.getWeight()+e2.getWeight());
e2=e2.getNext();
}
e1=e1.getNext();
}
return ans==Integer.MAX_VALUE/4?-1:ans;
}
public int[] twoHops(int index){
int cnt=0;
Edge e1=edges[index].getHead();
while(e1!=null){
Edge e2=edges[e1.getTo()].getHead();
while(e2!=null){cnt++;e2=e2.getNext();}
e1=e1.getNext();
}
int[] a=new int[cnt];int k=0;
e1=edges[index].getHead();
while(e1!=null){
Edge e2=edges[e1.getTo()].getHead();
while(e2!=null){
a[k++]=e2.getTo();
e2=e2.getNext();
}
e1=e1.getNext();
}
return a;
}
