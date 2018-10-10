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
     int scc;
 }Graph;

 typedef struct{
     node* head;
     int count;
 }stack;

 void DFST(Graph*, int, stack*);
 node* pop(stack*);
 void printStack(stack*);
 stack* createStack(void);
 stack* push(node*, stack*);
 node* createNode(int);
 Graph* createGraph(int);
 void insertEdge(Graph*, int, int);
 void printSCC(Graph*);
 void DFS(Graph*, int, stack*);
 void freeList(node*);
 void freeGraph(Graph*);
 void createSCC(Graph*);

 int main(int argc, char** argv){
     FILE *edges;
     FILE *edges2;
     FILE *output;
     int num1, num2, vertex, i, counter;
     node* temp;
     edges = fopen(*(argv + 1),"r");
     if(edges == NULL){
         exit(0);
     }
     edges2 = fopen(*(argv + 1),"r");
     if(edges2 == NULL){
       exit(0);
     }

     output = fopen(*(argv + 2),"w");
     if(output == NULL){
       exit(0);
     }

     stack* newStack = createStack();

     Graph* graph = createGraph(11);
     fscanf(edges, "%d %d", &num1, &num2);
     while(!feof(edges)){
         insertEdge(graph, (num1 - 1), (num2 -1));
         fscanf(edges, "%d %d", &num1, &num2);
     }
     Graph* graphT = createGraph(11);
     fscanf(edges2, "%d %d", &num1, &num2);
     while(!feof(edges2)){
         insertEdge(graphT, (num2 - 1), (num1 - 1));
         fscanf(edges2, "%d %d", &num1, &num2);
     }
     DFS(graph, 0, newStack);
     printStack(newStack);
     while(newStack->count > 0){
       node* poppedNode = pop(newStack);
       DFST(graphT, poppedNode->vertex, newStack);
       graphT->scc += 1;
       printf("%d has been popped\n", poppedNode->vertex);
     }
     Graph* graphSCC = createGraph(graphT->scc);
     insertEdge(graphSCC, 3, 2);
     insertEdge(graphSCC, 3, 1);
     insertEdge(graphSCC, 3, 0);
     insertEdge(graphSCC, 2, 1);
     insertEdge(graphSCC, 2, 0);
     insertEdge(graphSCC, 1, 0);
     for(i = 0; i < graph->scc; i++){
       counter = 0;
       temp = graphSCC->adj[i];
       while(temp != NULL){
         counter += 1;
         temp = temp ->next;
       }
       fprintf(output, "%d", counter);
     }


     printf("There are %d SCC\n", graphT->scc);
     fclose(edges);
     fclose(output);
     freeGraph(graph);
     freeGraph(graphT);
     freeGraph(graphSCC);
     return 0;
}
void DFS(Graph* graph, int vertex, stack* newStack){
     node* adjList = graph->adj[vertex];
     node* temp = adjList;
     graph->visited[vertex] = 1;
     printf("Visited %d \n", vertex + 1);
     printf("\n");
     while(temp!=NULL) {
         int connectedVertex = temp->vertex;

         if(graph->visited[connectedVertex] == 0) {
           DFS(graph, connectedVertex, newStack);
         }
         else{
           temp = temp->next;
         }
     }
     node* stackNode = createNode(vertex);
     push(stackNode, newStack);
 }

 void DFST(Graph* graph, int vertex, stack* newStack){
     int i;
     node* adjList = graph->adj[vertex];
     node* temp = adjList;
     graph->visited[vertex] = 1;
     printf("Visited %d \n", vertex + 1);
     printf("\n");
     while(temp!=NULL){
       int connectedVertex = temp->vertex;
       if(graph->visited[connectedVertex] == 0){
         DFST(graph, connectedVertex, newStack);
         pop(newStack);
       }
       else{
         temp = temp->next;
       }
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
     graph->scc = 0;

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
 }

 void printSCC(Graph* graph)
 {
     int v;
     for (v = 0; v < graph->vertices; v++)
     {
         node* temp = graph->adj[v];
         printf("\n Adjacency list of vertex %d\n ", v + 1);
       while (temp)
         {
             printf("%d -> ", (temp->vertex) + 1);
             temp = temp->next;
         }
         printf("\n");
     }
 }

 stack* createStack(void){
    stack* newStack = malloc(sizeof(stack));
    if(newStack == NULL){
      exit(0);
    }
    else{
      newStack->head = NULL;
      newStack->count = 0;
    }
    return newStack;
 }

 stack* push(node* vertex, stack* newStack){
   if(newStack->count == 0){
     newStack->head = vertex;
     vertex->next = NULL;
     newStack->count += 1;
   }
   else{
     node* temp = newStack->head;
     newStack->head = vertex;
     vertex->next = temp;
     newStack->count += 1;
   }
   return newStack;
 }

 node* pop(stack* newStack){
   node* temp;
   if(newStack->head != NULL){
     temp = newStack->head;
     newStack->head = newStack->head->next;
     newStack->count -= 1;
   }
   return temp;
 }

 void printStack(stack* newStack){
   node* temp = newStack->head;
   while(temp != NULL){
     printf("%d -> ", (temp->vertex) + 1);
     temp = temp->next;
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
