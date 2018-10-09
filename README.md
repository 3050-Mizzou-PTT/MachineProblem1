#include <stdio.h>
 #include <stdlib.h>
 typedef struct nodeStruct{
     int vertex;
   struct nodeStruct* next;
 }node;

 typedef struct{
     int vertices;
     node** adj;
     int* visited;
 }Graph;

 node* createNode(int);
 Graph* createGraph(int);
 void insertEdge(Graph*, int, int);
 void printSCC(Graph*);
 void DFS(Graph*, int);
 void freeList(node*);
 void freeGraph(Graph*);
 int main(int argc, char** argv){
     FILE *edges;
     //FILE *output;
     int num1, num2;
     edges = fopen(*(argv + 1),"r");
     if(edges == NULL){
         exit(0);
     }
     /*
      output = fopen(*(argv + 2),"w");
        if(output == NULL){
      exit(0);
        }
        */
     Graph* graph = createGraph(11);
     fscanf(edges, "%d %d", &num1, &num2);
     while(!feof(edges)){
         insertEdge(graph, (num1 - 1), (num2 -1));
         fscanf(edges, "%d %d", &num1, &num2);
     }
     Graph* graphT = createGraph(11);
     fscanf(edges, "%d %d", &num1, &num2);
     while(!feof(edges)){
         insertEdge(graphT, (num2 - 1), (num1 - 1));
         fscanf(edges, "%d %d", &num1, &num2);
     }
     printSCC(graph);
     DFS(graph, 0);
     fclose(edges);
     //fclose(output);
     freeGraph(graph);
     freeGraph(graphT);
     return 0;
}
void DFS(Graph* graph, int vertex){
     node* adjList = graph->adj[vertex];
     node* temp = adjList;
     graph->visited[vertex] = 1;
   printf("Visited %d \n", vertex);
     while(temp!=NULL) {
         int connectedVertex = temp->vertex;

         if(graph->visited[connectedVertex] == 0) {
             DFS(graph, connectedVertex);
         }
         temp = temp->next;
     }
 }

 node* createNode(int v){
     node* newNode = malloc(sizeof(node));
     newNode->vertex = v;
     newNode->next = NULL;
     return newNode;
 }

 Graph* createGraph(int num){
   Graph* graph = malloc(sizeof(Graph));
     graph->vertices = num;

     graph->adj = malloc(num * sizeof(node*));

     graph->visited = malloc(num * sizeof(int));

     int i;
     for (i = 0; i < num; i++) {
         graph->adj[i] = NULL;
         graph->visited[i] = 0;
     }
     return graph;
 }
void insertEdge(Graph* graph, int num1, int num2)
 {
     node* newNode = createNode(num2);
     newNode->next = graph->adj[num1];
     graph->adj[num1] = newNode;

     newNode = createNode(num1);
     newNode->next = graph->adj[num2];
     graph->adj[num2] = newNode;
 }

 void printSCC(Graph* graph)
 {
     int v;
     for (v = 0; v < graph->vertices; v++)
     {
         node* temp = graph->adj[v];
         printf("\n Adjacency list of vertex %d\n ", v);
       while (temp)
         {
             printf("%d -> ", temp->vertex);
             temp = temp->next;
         }
         printf("\n");
     }
 }

 void freeList(node* new){
     node* hold;
     while(new != NULL){
         hold = new;
         new = new->next;
         free(hold);
     }
 }
 void freeGraph(Graph* newGraph){
     int i;
     for(i = 0; i < newGraph->vertices; i++){
         freeList(newGraph->adj[i]);
     }
     free(newGraph);
 }
