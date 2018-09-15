# MachineProblem1
#include <stdio.h>
#include <stdlib.h>

struct node
{
    int vertex;
    struct node* next;
};
struct node* createNode(int);

struct Graph
{
    int numVertices;
    struct node** adjLists;
};

struct Graph* createGraph(int vertices);
void addEdge(struct Graph* graph, int src, int dest);
void writeGraph(struct Graph* graph);

int main()
{
    struct Graph* graph = createGraph(4);
    addEdge(graph, 1, 2);
    addEdge(graph, 2, 3);
    addEdge(graph, 3, 1);
    addEdge(graph, 4, 1);
    addEdge(graph, 2, 4);
    addEdge(graph, 4, 3);

    writeGraph(graph);

    return 0;
}


struct node* createNode(int v){
    struct node* newNode = malloc(sizeof(struct node));
    newNode->vertex = v;
    newNode->next = NULL;
    return newNode;
}

struct Graph* createGraph(int vertices){

    struct Graph* graph = malloc(sizeof(struct Graph));
    graph->numVertices = vertices;

    graph->adjLists = malloc(vertices * sizeof(struct node*));

    int i;
    for (i = 0; i < vertices; i++)
        graph->adjLists[i] = NULL;

    return graph;
}

void addEdge(struct Graph* graph, int src, int dest)
{
    struct node* newNode = createNode(dest);
    newNode->next = graph->adjLists[src];
    graph->adjLists[src] = newNode;

    newNode = createNode(src);
    newNode->next = graph->adjLists[dest];
    graph->adjLists[dest] = newNode;
}

void writeGraph(struct Graph* graph)
{
    int v;
    for (v = 1; v <= graph->numVertices; v++)
    {
        struct node* temp = graph->adjLists[v];
        printf("\n Adjacency list of vertex %d\n ", v);
        while (temp)
        {
            printf("%d -> ", temp->vertex);
            temp = temp->next;
        }
        printf("\n");
    }
}
