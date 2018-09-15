# MachineProblem1
#include <stdio.h>
#include <stdlib.h>

typedef struct nodeStruct
{
    int vertex;
    struct nodeStruct* next;
} node;

typedef struct
{
    int numVertices;
    node** adjLists;
}Graph;

Graph* createGraph(int);
void insertEdge(Graph*, int, int);
void writeGraph(Graph*, FILE*);
node* createNode(int);
node* orderedList(Graph*, int);

int main(int argc, char**argv)
{
  int num1, num2;
  FILE *edges;
  FILE *output;

  edges = fopen(*(argv + 1), "r");
  if(edges == NULL){
    exit(0);
  }
  output = fopen(*(argv + 2), "w");

    Graph* graph = createGraph(4);
    fscanf(edges,"%d %d", &num1, &num2);
    while(!feof(edges)){
      insertEdge(graph,num1,num2);
      printf("%d %d\n", num1,num2);
      fscanf(edges,"%d %d", &num1, &num2);
    }

    writeGraph(graph, output);

    fclose(edges);
    fclose(output);

    return 0;
}


node* createNode(int v){
    node* newNode = malloc(sizeof(node));
    newNode->vertex = v;
    newNode->next = NULL;
    return newNode;
}

Graph* createGraph(int vertices){

    Graph* graph = malloc(sizeof(Graph));
    graph->numVertices = vertices;

    graph->adjLists = malloc(vertices * sizeof(node*));

    int i;
    for (i = 0; i < vertices; i++)
        graph->adjLists[i] = NULL;

    return graph;
}

void insertEdge(Graph* graph, int num1, int num2)
{
    node* newNode = createNode(num1);
    newNode->next = graph->adjLists[num2];
    graph->adjLists[num2] = newNode;

    newNode = createNode(num2);
    newNode->next = graph->adjLists[num1];
    graph->adjLists[num1] = newNode;
}
node* orderedList(Graph* graph, int vertex){
    node* num1 = graph->adjLists[vertex];
    int temp;
    node* num2;
    node* hold = num1;
    while(graph->adjLists[vertex]->next != NULL){
      num2 = graph->adjLists[vertex]->next;
      if(graph->adjLists[vertex]->next->vertex < num1->vertex){
        temp = num1->vertex;
        num1->vertex = num2->vertex;
        num2->vertex = temp;
      }
      graph->adjLists[vertex] = graph->adjLists[vertex]->next;
    }
    return hold;
}


void writeGraph(Graph* graph, FILE *output)
{
    int v;
    for (v = 1; v <= graph->numVertices; v++)
    {
        node* temp = orderedList(graph, v);
        fprintf(output,"%d:",v);

        while (temp)
        {
            fprintf(output,"%d -> ", temp->vertex);
            temp = temp->next;
        }
        fprintf(output,"\n");
    }
}
