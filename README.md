# lab-assign-07
#include <iostream>
#include <vector>
#include <algorithm>
#include <limits.h>

using namespace std;

// Edge structure for Kruskal's algorithm
struct Edge {
    int src, dest, weight;
};

// Graph structure
class Graph {
public:
    int V; // Number of vertices
    vector<Edge> edges; // Collection of edges

    Graph(int V);
    void addEdge(int src, int dest, int weight);
    void kruskalMST();
    void primMST();
};

// Disjoint Set Union (DSU) for Kruskal's algorithm
class DisjointSet {
public:
    vector<int> parent, rank;

    DisjointSet(int n);
    int find(int u);
    void unionSet(int u, int v);
};

// Constructor for Graph
Graph::Graph(int V) {
    this->V = V;
}

// Method to add an edge to the graph
void Graph::addEdge(int src, int dest, int weight) {
    edges.push_back({src, dest, weight});
}

// Function to compare edges (used for sorting)
bool compareEdges(Edge a, Edge b) {
    return a.weight < b.weight;
}

// Kruskal's algorithm to find MST
void Graph::kruskalMST() {
    sort(edges.begin(), edges.end(), compareEdges);
    DisjointSet ds(V);
    vector<Edge> result;

    for (Edge edge : edges) {
        int u = edge.src;
        int v = edge.dest;
        if (ds.find(u) != ds.find(v)) {
            ds.unionSet(u, v);
            result.push_back(edge);
        }
    }

    cout << "Kruskal's MST:\n";
    for (Edge edge : result) {
        cout << edge.src << " -- " << edge.dest << " == " << edge.weight << endl;
    }
}

// Constructor for Disjoint Set Union
DisjointSet::DisjointSet(int n) {
    parent.resize(n);
    rank.resize(n, 0);
    for (int i = 0; i < n; i++) {
        parent[i] = i;
    }
}

// Find with path compression
int DisjointSet::find(int u) {
    if (u != parent[u]) {
        parent[u] = find(parent[u]);
    }
    return parent[u];
}

// Union by rank
void DisjointSet::unionSet(int u, int v) {
    int root_u = find(u);
    int root_v = find(v);
    if (root_u != root_v) {
        if (rank[root_u] < rank[root_v]) {
            parent[root_u] = root_v;
        } else if (rank[root_u] > rank[root_v]) {
            parent[root_v] = root_u;
        } else {
            parent[root_v] = root_u;
            rank[root_u]++;
        }
    }
}

// Prim's algorithm to find MST
void Graph::primMST() {
    vector<int> key(V, INT_MAX);
    vector<bool> inMST(V, false);
    vector<int> parent(V, -1);
    key[0] = 0; // Starting from the first vertex

    for (int count = 0; count < V - 1; count++) {
        int u = -1;
        for (int i = 0; i < V; i++) {
            if (!inMST[i] && (u == -1 || key[i] < key[u])) {
                u = i;
            }
        }

        inMST[u] = true;

        for (const Edge& edge : edges) {
            if (edge.src == u && !inMST[edge.dest] && edge.weight < key[edge.dest]) {
                key[edge.dest] = edge.weight;
                parent[edge.dest] = u;
            }
        }
    }

    cout << "Prim's MST:\n";
    for (int i = 1; i < V; i++) {
        cout << parent[i] << " -- " << i << " == " << key[i] << endl;
    }
}

int main() {
    // Create a graph with 4 departments
    Graph g(4);
    g.addEdge(0, 1, 2); // CS - EE
    g.addEdge(0, 2, 3); // CS - ME
    g.addEdge(1, 3, 5); // EE - CE
    g.addEdge(2, 3, 6); // ME - CE

    // Find MST using Kruskal's algorithm
    g.kruskalMST();

   
