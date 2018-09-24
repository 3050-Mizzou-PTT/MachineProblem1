   /*
      Contributors - Payton Boekhout - plb4dq - Created the writeintoGraph and orderedList function
                   - John Burkin - jtb4dk - Created the insertEdge, createGraph, and createNode functions
                   - Tim Kwon - jk4g2 - Created main, as well as both freeList, and freeGraph functions, also created header file
                   
      It runs in linear time because createGraph takes O(V) time, createNode takes O(1) time, insertEdge takes O(1) time, orderedList function takes O(V + E) time, writeintoGraph takes O(V + E) time as well, freeList takes O(E) time and freeGraph takes O(V + E) meaning that the whole running time is proportional to O(V + E).
  
   */
   #include <stdio.h>
   #include <stdlib.h>
   typedef struct nodeStruct{
       int vertex;
       struct nodeStruct* next;
   }node;
   typedef struct{
       int vertices;
       node** adj;
  }Graph;
  
  Graph* createGraph(int);
  void insertEdge(Graph*, int, int);
  void writeintoGraph(Graph*, FILE*);
  node* createNode(int);
  node* orderedList(Graph*, int);
  void freeList(node*);
  void freeGraph(Graph*);
  
  int main(int agrc, char** argv){
      int num1, num2;
      FILE *edges;
      FILE *output;
  
      edges = fopen(*(argv + 1),"r");
      if(edges == NULL){
          exit(0);
      }
      output = fopen(*(argv + 2),"w");
      if(output == NULL){
          exit(0);
      }
      Graph* newGraph = createGraph(4);
      fscanf(edges, "%d %d", &num1, &num2);
      while(!feof(edges)){
          insertEdge(newGraph,num1,num2);
          fscanf(edges, "%d %d", &num1, &num2);
      }
      writeintoGraph(newGraph,output);
      fclose(edges);
      fclose(output);
      freeGraph(newGraph);
      return 0;
  }
  
 Graph* createGraph(int numofv){
      int i;
      Graph* newGraph = malloc(sizeof(Graph));
      if(newGraph == NULL){
         exit(0);
      }
      newGraph->vertices = numofv;
      newGraph->adj = malloc(numofv * sizeof(node*));
      if(newGraph->adj == NULL){
         exit(0);
      }
      for(i = 0; i < numofv; i++){
          newGraph->adj[i] = NULL;
      }
      return newGraph;
  }
  
 node* createNode(int vertex){
     node* new = malloc(sizeof(node));
     if(new == NULL){
      exit(0);
     }
     new->vertex = vertex;
     new->next = NULL;
     return new;
 }

void insertEdge(Graph* newGraph, int num1, int num2){
     node* new = createNode(num1);
     new->next = newGraph->adj[num2];
     newGraph->adj[num2] = new;
     new = createNode(num2);
     new->next = newGraph->adj[num1];
     newGraph->adj[num1] = new;
  }
  
 node* orderedList(Graph* newGraph, int vert){
      int temp, temp2;
      node* hold = newGraph->adj[vert];
      node* hold2 = newGraph->adj[vert];
      while(newGraph->adj[vert]->next != NULL){
          if(newGraph->adj[vert]->vertex > newGraph->adj[vert]->next->vertex){
              temp = newGraph->adj[vert]->vertex;
              newGraph->adj[vert]->vertex = newGraph->adj[vert]->next->vertex;
              newGraph->adj[vert]->next->vertex = temp;
          }
          newGraph->adj[vert] = newGraph->adj[vert]->next;
      }
      while(hold2->next != NULL){
          if(hold2->vertex > hold2->next->vertex){
              temp2 = hold2->vertex;
              hold2->vertex = hold2->next->vertex;
              hold2->next->vertex = temp2;
          }
          hold2 = hold2->next;
      }
      return hold;
  }
  
  void writeintoGraph(Graph* newGraph, FILE *output){
      int v;
      for(v = 1; v <= newGraph->vertices; v++){
         node* temp = orderedList(newGraph, v);
         fprintf(output, "%d:",v);
         while(temp){
             fprintf(output,"%d\t ", temp->vertex);
             temp = temp->next;
         }
         fprintf(output, "\n");
         freeList(temp);
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
                               
