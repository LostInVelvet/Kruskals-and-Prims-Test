#include <iostream>
#include <cstdlib>		// qsort
#include <cstring>		// NULL
#include <climits>		// INT_MAX
#include <vector>

using namespace std;

const int INFINITY = INT_MAX;	// Used for prims
const int NINFINITY = INT_MIN;	


// ****************************************************************
//
//			      Edge
//
// ****************************************************************



struct Node{
    int src;
    int dest;
    int weight;
    Node* next;
};



// ****************************************************************
//
//                            List
//
// ****************************************************************



struct List{
  int size;
  Node* head;  
};



// ****************************************************************
//
//                           Graph
//
// ****************************************************************



class Graph{
  public:
    int vertices;
    int edges;
  
    Node* edge;
    List* array;

    Node* add_edge(const int node, int src, int dest, int w){
      Node* newEdge = new Node;
      newEdge->src = src;
      newEdge->dest = dest;
      newEdge->weight = w;
      newEdge->next = NULL;

      return newEdge;
    }

    void add_node(const int node, int src, int dest, int w){
      Node* newEdge = add_edge(node, src, dest, w);
      
      newEdge->next = array[src].head;
      array[src].head = newEdge;
      
      newEdge = add_edge(node, dest, src, w);
      
      newEdge->next = array[dest].head;
      array[dest].head = newEdge;
      
      edge[node] = *newEdge;
    }
    

    int get_vertices(){ return vertices; }
    int get_edges() { return edges; }
    Node* get_edge(){ return edge; }
    Node get_single_edge(int num){ return edge[num]; }
};



// ****************************************************************



struct Graph* create_graph(int v, int e){
  Graph* graph = (Graph*)malloc(sizeof(Graph));

  graph->vertices = v;
  graph->edges = e;
  
  graph->edge = (Node*)malloc(graph->edges * sizeof(Node));
  graph->array = (List*)malloc(graph->edges * sizeof(List));
  
  for(int i = 0; i < graph->vertices; i++){
    graph->array[i].head = NULL;
  }
  
  return graph;
}



// ****************************************************************
//
//                        Other Helpers
//
// ****************************************************************



struct Subtree{
  int head;
  int numElements;
};


struct PrimHelper{
  int key;
  int parent;
};



// ****************************************************************

// ****************************************************************



void Kruskals(Graph* graph);
void Prims(Graph* graph);

int find(Subtree subtrees[], int findMe);
void Union(Subtree subtrees[], int v1, int v2);
int Compare(const void * a, const void * b);

int GetMin(Graph g, PrimHelper t[5], int q[5], vector<pair<int, int> > &MST);
void UpdateKeys(Graph g, int q[5], PrimHelper t[5], int value);
bool IsAvailable(int q[5], int value);
void Reorder(vector<pair<int, int> > &MST);



// ****************************************************************
//
//			     Main
//
// ****************************************************************



int main(){
  int numV = 5;
  int numE = 7;
  Graph *g = create_graph(numV, numE);
  

/*
       (2)
    5 /   \ 4
     /     \
   (1)--9--(3)
    |      /|
    |    /  |
   -7   2  -2
    |  /    |
    |/      |
   (0)--0--(4)
        

*/


  g->add_node(0, 0, 1, -7);
  g->add_node(1, 0, 3, 2);
  g->add_node(2, 0, 4, 0);
  g->add_node(3, 1, 2, 5);
  g->add_node(4, 1, 3, 9);
  g->add_node(5, 2, 3, 4);
  g->add_node(6, 3, 4, -2);
  
  Kruskals(g);
  Prims(g);

  return 0;
}



// ****************************************************************
//
//			Kruskal's Algoritm
//
// ****************************************************************



// Implements Kruskal's Algorithm
// Pre: Graph is made and filled
// Post: Kruskal's Algorithm is implemented and the result is output
void Kruskals(Graph* graph){
  Graph* g = graph;
  int v = g->get_vertices();
  Node result[v];		// Stores the result of the MST
  int numEdges = 0;
  int sortedEdges = 0;


  // Sort all the edges in order by weight using quicksort
  qsort(g->get_edge(), g->get_edges(), sizeof(g->get_single_edge(0)), Compare);


  // Test the sorted graph
/*  cout << endl << "Testing Graph: ";

  for(int i = 0; i < 7; i++)
    cout << endl << g->edge[i].src << " " << g->edge[i].dest << " " <<  g->edge[i].weight;
  cout << endl << endl;
*/
  
  // Allocate Memory
  Subtree *subtrees = (Subtree*)malloc( v * sizeof(Subtree) );

  
  // Create v Subtrees
  for(int i = 0; i < v; i++){
    subtrees[i].head = i;
    subtrees[i].numElements = 0;
  }
  

  
  while(numEdges < v-1){
    Node next = g->get_single_edge(sortedEdges);
    
    int src = find(subtrees, next.src);
    int dest = find(subtrees, next.dest);

    if(src != dest){
      result[numEdges++] = next;
      Union(subtrees, src, dest);
    }
    sortedEdges++;
  }
  
  /*
  cout << endl << "Kruskals: " << endl;
  for(int i = 0; i < numEdges; i++){

    cout << endl << result[i].src << " -> " << result[i].dest;
  }
  cout << endl << endl;
*/
}



// ****************************************************************
//
//			  Prim's Algorithm
//
// ****************************************************************



// Prim's Algorithm
// Pre: Graph is made and filled
// Post: Prim's algorithm is implemented and the result is output
void Prims(Graph* graph){
  Graph g = *graph;
  int V = g.vertices;
  vector<pair<int, int> > MST;     // Holds the Visited Nodes
  int q[V];
  
  PrimHelper t[V];
  int lastNode = 0;		// Holds the integer value of the last node visited
  
  // Set up Q
  for(int i = 0; i < V; i++){
    q[i] = i;
  }
  
  // Set all keys to infinity and all parents to NULL
  for(int i = 0; i<V; i++){
    t[i].key = INFINITY;
    t[i].parent = NINFINITY;
  }
  
  // Pick starting node 0 and set it's key to 0
  t[0].key = 0;

  for(int i = 0; i < V; i++){  
    // Get Minimum
    lastNode = GetMin(g, t, q, MST);

    // Update keys nearby
    UpdateKeys(g, q, t, lastNode);
    
    q[lastNode] = NINFINITY;
  }


  cout << "Prims:" << endl << endl;

  // Re-order the vector
  Reorder(MST);
  
  // Start at 1 to skip the 0
/*  for(int i = 1; i < MST.size(); i++){
    cout << MST[i].first << " -> " << MST[i].second << endl;
  }
  cout << endl;
*/

  
//  cout << "Vector: " << MST[0].first << " -> " << MST[0].second << endl;


      // Debug: Print Out the Keys
/*      for(int i = 0; i < V; i++){
        cout << "Key " << i << ": " << t[i].key << endl;
      }
*/

  // Debug: Output the Adjacency list
  Node* temp;
  for(int i = 0; i < V; i++){
    temp = g.array[i].head;
    cout << i << ": " ;
    while(temp){
      cout << " -> (" << temp->src << ", " << temp->dest << ")";
      temp = temp->next;
    }
    cout << endl;
  }


}



// ****************************************************************
//
//		      Kruskal Helper Functions
//
// ****************************************************************



// Finds the head of the subtree with the specified element
// Pre: Subtrees are made and the desired element is specified
// Post: Finds the head of the desired subtree
int find(Subtree subtrees[], int findMe) {

  if(subtrees[findMe].head != findMe)
    subtrees[findMe].head = find(subtrees, subtrees[findMe].head);

  return subtrees[findMe].head;
}



// ****************************************************************



// Combines two subtrees
// Pre: Subtrees are made and elements are defined
// Post: Combines the subtrees into one subtree
void Union(Subtree subtrees[], int v1, int v2){
  int vt1 = find(subtrees, v1);
  int vt2 = find(subtrees, v2);



// Find the tree with the lower amount of elements and combine it to the bigger one

  // If vt1 has more elements than vt2
  // Add vt1 to vt2
  if(subtrees[vt1].numElements < subtrees[vt2].numElements){
    subtrees[vt1].head = vt2;
  }

  // If vt1 has less elements than vt2
  // Add vt2 to vt1
  else if(subtrees[vt1].numElements > subtrees[vt2].numElements){
    subtrees[vt2].head = vt1;
  }

  // If vt1 = vt2
  // Then set vt2's head to vt1
  else {
    subtrees[vt2].head = vt1;
    subtrees[vt1].numElements++;
  }

}



// ****************************************************************



// Used for the qsort function
int Compare(const void * a, const void * b){
  Node * a1 = (Node*)a;
  Node * b1 = (Node*)b;

  return a1->weight > b1->weight;
}


// ****************************************************************
//
//                      Prim Helper Functions
//
// ****************************************************************


// Finds the minimum key value in t and returns the node's number
// Pre: Graph is made and keys are updated
// Post: Minimum key is found in t. The parent's number is returned
int GetMin(Graph g, PrimHelper t[5], int q[5], vector<pair<int, int> > &MST){
  int min = INFINITY;
  int parent;
  int minVal = INFINITY;
  
  // Get Minimum
  for(int i = 0; i < g.vertices; i++){
    if(t[i].key < minVal && IsAvailable(q, i) == true){
      min = i;
      minVal = t[i].key;
      parent = t[i].parent;
    }
  }
//  cout << "Vector added: " << parent << ", " << min << endl;
  MST.push_back(make_pair(parent, min) );
  
  return min;
}



// ****************************************************************



// t is updated with the weights adjacent to node 'value'
// Pre: Graph is made and the list of available elements (q) is updated
// Post: Updates t to hold the weights of all adjacent elements to 'value'
void UpdateKeys(Graph g, int q[5], PrimHelper t[5], int value){
  Node* temp = new Node;
  int nodeNeeded;
    
  temp = g.array[value].head;

  while(temp != NULL){
    // Find the source/destination
    nodeNeeded = temp->dest;

    // Check if it's less than key[ ]
    if(temp->weight < t[nodeNeeded].key && IsAvailable(q, temp->src)){
      // If so, update the key
      t[nodeNeeded].key = temp->weight;
      t[nodeNeeded].parent = value;
    }
    temp = temp->next;
  }      
}



// ****************************************************************



// Checks if an element is available (to avoid a cycle)
// Pre: q is filled with elements available or set to NEGINFINITY if
//	not available
// Post: Returns whether the specified value is available
bool IsAvailable(int q[5], int value){
//  cout << value <<" is available: ";
  if(q[value] != NINFINITY){
//    cout << "true" << endl;
    return true;
  }
  else {
//    cout << "false" << endl;
    return false;
  }

}



// ****************************************************************



// Re-orders the vector to be the correct way
// Pre: MST is filled
// Post: Correctly ordered MST is filled
void Reorder(vector<pair<int, int> > &MST){
  vector<pair<int, int> > T;

  for(int i = 0; i < MST.size(); i++){
    T.push_back(MST[i]);
  }

  MST = T;
}
