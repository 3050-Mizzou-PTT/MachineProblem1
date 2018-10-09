   #include <stdio.h>
  2 #include <stdlib.h> 
  3  
  4 typedef struct nodeStruct{ 
  5     int vertex; 
  6     struct nodeStruct* next; 
  7 }node; 
  8  
  9 typedef struct{ 
 10     int vertices; 
 11     node** adj; 
 12     int* visited; 
 13 }Graph; 
 14  
 15 node* createNode(int); 
 16 Graph* createGraph(int); 
 17 void insertEdge(Graph*, int, int); 
 18 void printSCC(Graph*); 
 19 void DFS(Graph*, int); 
 20 void freeList(node*); 
 21 void freeGraph(Graph*); 
 22  
 23 int main(int argc, char** argv){ 
 24     FILE *edges; 
 25     //FILE *output; 
 26     int num1, num2; 
 27     edges = fopen(*(argv + 1),"r"); 
 28     if(edges == NULL){ 
 29         exit(0); 
 30     } 
 31     /* 
 32        output = fopen(*(argv + 2),"w"); 
 33        if(output == NULL){ 
 34        exit(0); 
 35        } 
 36        */ 
 37     Graph* graph = createGraph(11); 
 38     fscanf(edges, "%d %d", &num1, &num2); 
 39     while(!feof(edges)){ 
 40         insertEdge(graph, (num1 - 1), (num2 -1)); 
 41         fscanf(edges, "%d %d", &num1, &num2); 
 42     } 
 43     Graph* graphT = createGraph(11); 
 44     fscanf(edges, "%d %d", &num1, &num2); 
 45     while(!feof(edges)){ 
 46         insertEdge(graphT, (num2 - 1), (num1 - 1)); 
 47         fscanf(edges, "%d %d", &num1, &num2); 
 48     } 
 49     printSCC(graph); 
 50     DFS(graph, 0); 
 51     fclose(edges); 
 52     //fclose(output); 
 53     freeGraph(graph); 
 54     freeGraph(graphT); 
 55     return 0;
 }
 
 58 void DFS(Graph* graph, int vertex){
 59     node* adjList = graph->adj[vertex];
 60     node* temp = adjList;
 61 
 62     graph->visited[vertex] = 1;
 63     printf("Visited %d \n", vertex);
 64 
 65     while(temp!=NULL) {
 66         int connectedVertex = temp->vertex;
 67 
 68         if(graph->visited[connectedVertex] == 0) {
 69             DFS(graph, connectedVertex);
 70         }
 71         temp = temp->next;
 72     }
 73 }
 74 
 76 node* createNode(int v){
 77     node* newNode = malloc(sizeof(node));
 78     newNode->vertex = v;
 79     newNode->next = NULL;
 80     return newNode;
 81 }
 82 
 83 Graph* createGraph(int num){
 84     Graph* graph = malloc(sizeof(Graph));
 85     graph->vertices = num;
 86 
 87     graph->adj = malloc(num * sizeof(node*));
 88 
 89     graph->visited = malloc(num * sizeof(int));
 90 
 91     int i;
 92     for (i = 0; i < num; i++) {
 93         graph->adj[i] = NULL;
 94         graph->visited[i] = 0;
 95     }
 96     return graph;
 97 }
 98 
 99 void insertEdge(Graph* graph, int num1, int num2)
100 {
101     node* newNode = createNode(num2);
102     newNode->next = graph->adj[num1];
103     graph->adj[num1] = newNode;
104 
105     newNode = createNode(num1);
106     newNode->next = graph->adj[num2];
107     graph->adj[num2] = newNode;
108 }

110 void printSCC(Graph* graph)
111 {
112     int v;
113     for (v = 0; v < graph->vertices; v++)
114     {
115         node* temp = graph->adj[v];
116         printf("\n Adjacency list of vertex %d\n ", v);
117         while (temp)
118         {
119             printf("%d -> ", temp->vertex);
120             temp = temp->next;
121         }
122         printf("\n");
123     }
124 }
125 
126 void freeList(node* new){
127     node* hold;
128     while(new != NULL){
129         hold = new;
130         new = new->next;
131         free(hold);
132     }
133 }
134 
135 void freeGraph(Graph* newGraph){
136     int i;
137     for(i = 0; i < newGraph->vertices; i++){
138         freeList(newGraph->adj[i]);
139     }
140     free(newGraph);
141 }                                                                                                                           
