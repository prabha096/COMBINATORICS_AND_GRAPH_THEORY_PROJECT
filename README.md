#include <stdio.h>
#include <stdlib.h>
#define INF 9999
#define MAX 20

int graph[MAX][MAX], n;


void bfs(int start) {
    int visited[MAX] = {0};
    int queue[MAX], front = 0, rear = 0;
    visited[start] = 1;
    queue[rear++] = start;

    printf("Friends-of-friends of User %d: ", start);

    while (front < rear) {
        int node = queue[front++];
        for (int i = 0; i < n; i++) {
            if (graph[node][i] && !visited[i]) {
                visited[i] = 1;
                queue[rear++] = i;
                printf("%d ", i);
            }
        }
    }
    printf("\n");
}

void dfsUtil(int v, int visited[]) {
    visited[v] = 1;
    printf("%d ", v);
    for (int i = 0; i < n; i++) {
        if (graph[v][i] && !visited[i])
            dfsUtil(i, visited);
    }
}

void dfs() {
    int visited[MAX] = {0}, count = 0;
    printf("\nCommunities in Network:\n");
    for (int i = 0; i < n; i++) {
        if (!visited[i]) {
            count++;
            printf("Community %d: ", count);
            dfsUtil(i, visited);
            printf("\n");
        }
    }
}


void dijkstra(int start) {
    int cost[MAX][MAX], distance[MAX], visited[MAX], pred[MAX];
    int count, mindistance, nextnode;

    for (int i = 0; i < n; i++)
        for (int j = 0; j < n; j++)
            cost[i][j] = (graph[i][j] == 0 && i != j) ? INF : graph[i][j];

    for (int i = 0; i < n; i++) {
        distance[i] = cost[start][i];
        pred[i] = start;
        visited[i] = 0;
    }

    distance[start] = 0;
    visited[start] = 1;
    count = 1;

    while (count < n - 1) {
        mindistance = INF;
        for (int i = 0; i < n; i++)
            if (distance[i] < mindistance && !visited[i]) {
                mindistance = distance[i];
                nextnode = i;
            }
        visited[nextnode] = 1;

        for (int i = 0; i < n; i++)
            if (!visited[i])
                if (mindistance + cost[nextnode][i] < distance[i]) {
                    distance[i] = mindistance + cost[nextnode][i];
                    pred[i] = nextnode;
                }
        count++;
    }

    printf("\nShortest connection paths from user %d:\n", start);
    for (int i = 0; i < n; i++) {
        if (i != start && distance[i]!=INF) {
            printf("To %d: Distance = %d | Path: %d", i, distance[i], i);
            int j = i;
            while (j != start) {
                j = pred[j];
                printf("<-%d", j);
            }
            printf("\n");
        }
    }
}
int parent[MAX];
int findp(int x) {

        while (parent[x] != x)
            x = parent[x];
        return x;
    }
void kruskal() {
    int a, b, u, v, edges = 0, min, total = 0, compCount = 0;
    int edgeCount = 0;

    
    for (int i = 0; i < n; i++)
        parent[i] = i;
    printf("\nEdges in Minimum Spanning Forest(min connection network) (Kruskal's):\n");

    while (edges < n - 1) {
        min = INF;
        u = v = -1;

        
        for (int i = 0; i < n; i++) {
            for (int j = 0; j < n; j++) {
                if (graph[i][j] && findp(i) != findp(j) && graph[i][j] < min) {
                    min = graph[i][j];
                    u = i;
                    v = j;
                }
            }
        }

        if (u == -1 || v == -1)
            break; 

        int pu = findp(u);
        int pv = findp(v);

        parent[pv] = pu;   
        printf("Edge %d: %d - %d (cost %d)\n", ++edgeCount, u, v, min);
        total += min;
        edges++;
    }

    
    int comp[MAX] = {0};
    for (int i = 0; i < n; i++)
        comp[findp(i)] = 1;

    printf("\nComponent MSTs:\n");
    for (int i = 0; i < n; i++) {
        if (comp[i]) {
            printf("Component rooted at %d has MST\n", i);
            compCount++;
        }
    }

    printf("Total Components = %d\n", compCount);
}


int main() {
    int choice, start;

    printf("Enter number of users in the network: ");
    scanf("%d", &n);

    printf("Enter adjacency matrix (0 if no connection):\n");
    for (int i = 0; i < n; i++)
        for (int j = 0; j < n; j++)
            scanf("%d", &graph[i][j]);

    do {
        printf("\n---- Social Network Analysis ----\n");
        printf("1. BFS - Friends-of-Friends\n");
        printf("2. DFS - Find Communities\n");
        printf("3. Dijkstra - Shortest Connection Path\n");
        printf("4. MST - Minimal Connection Network\n");
        printf("5. Exit\n");
        printf("Enter your choice: ");
        scanf("%d", &choice);

        switch (choice) {
        case 1:
            printf("Enter starting user: ");
            scanf("%d", &start);
            bfs(start);
            break;
        case 2:
            dfs();
            break;
        case 3:
            printf("Enter starting user: ");
            scanf("%d", &start);
            dijkstra(start);
            break;
        case 4:
            kruskal();
            break;
        case 5:
            printf("Exiting...\n");
            break;
        default:
            printf("Invalid choice!\n");
        }
    } while (choice != 5);

    return 0;
}
