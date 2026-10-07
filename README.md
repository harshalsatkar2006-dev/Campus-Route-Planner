#include <stdio.h>

#define MAX 20
#define INF 99999

int graph[MAX][MAX];
int n;
char name[MAX][50];

/* Display adjacency matrix */
void displayMatrix()
{
    int i, j;

    printf("\nAdjacency Matrix:\n");

    for (i = 0; i < n; i++)
    {
        for (j = 0; j < n; j++)
        {
            if (graph[i][j] == INF)
                printf("%7s", "INF");
            else
                printf("%7d", graph[i][j]);
        }
        printf("\n");
    }
}

/* Enter campus graph */
void enterGraph()
{
    int i, j, w;

    printf("Enter number of locations: ");
    scanf("%d", &n);

    for (i = 0; i < n; i++)
    {
        printf("Enter location %d: ", i + 1);
        scanf(" %49[^\n]", name[i]);
    }

    printf("\nEnter distances between locations.\n");
    printf("Enter -1 if there is no direct road.\n");

    for (i = 0; i < n; i++)
    {
        for (j = 0; j < n; j++)
        {
            printf("%s to %s: ", name[i], name[j]);
            scanf("%d", &w);

            if (i == j)
                graph[i][j] = 0;
            else if (w == -1)
                graph[i][j] = INF;
            else
                graph[i][j] = w;
        }
    }
}

/* Find vertex with minimum distance */
int minVertex(int dist[], int visited[])
{
    int i, min = INF, vertex = -1;

    for (i = 0; i < n; i++)
    {
        if (!visited[i] && dist[i] < min)
        {
            min = dist[i];
            vertex = i;
        }
    }

    return vertex;
}

/* Display shortest path recursively */
void printPath(int parent[], int v)
{
    if (parent[v] == -1)
    {
        printf("%s", name[v]);
        return;
    }

    printPath(parent, parent[v]);
    printf(" -> %s", name[v]);
}

/* Dijkstra's algorithm */
void dijkstra(int source, int showPath)
{
    int dist[MAX], parent[MAX], visited[MAX] = {0};
    int i, j, u;

    for (i = 0; i < n; i++)
    {
        dist[i] = INF;
        parent[i] = -1;
    }

    dist[source] = 0;

    for (i = 0; i < n; i++)
    {
        u = minVertex(dist, visited);

        if (u == -1)
            break;

        visited[u] = 1;

        for (j = 0; j < n; j++)
        {
            if (!visited[j] &&
                graph[u][j] != INF &&
                dist[u] != INF &&
                dist[u] + graph[u][j] < dist[j])
            {
                dist[j] = dist[u] + graph[u][j];
                parent[j] = u;
            }
        }
    }

    printf("\nSource Location: %s\n", name[source]);

    printf("\n%-25s %-15s %s\n",
           "Destination", "Distance", "Shortest Path");

    for (i = 0; i < n; i++)
    {
        if (i == source)
            continue;

        if (dist[i] == INF)
        {
            printf("%-25s %-15s %s\n",
                   name[i], "Unreachable", "-");
        }
        else
        {
            printf("\nDestination: %s\n", name[i]);
            printf("Shortest Distance: %d\n", dist[i]);

            if (showPath)
            {
                printf("Shortest Path: ");
                printPath(parent, i);
                printf("\n");
            }
        }
    }
}

/* Main function */
int main()
{
    int choice, source = 0;
    int entered = 0, i;

    do
    {
        printf("\n===== CAMPUS SHORTEST ROUTE FINDER =====\n");
        printf("1. Enter Campus Graph\n");
        printf("2. Display Adjacency Matrix\n");
        printf("3. Select Source Location\n");
        printf("4. Find Shortest Distance\n");
        printf("5. Display Shortest Paths\n");
        printf("6. Display Distance from Source to All Locations\n");
        printf("7. Exit\n");

        printf("Enter your choice: ");
        scanf("%d", &choice);

        switch (choice)
        {
            case 1:
                enterGraph();
                entered = 1;
                source = 0;
                break;

            case 2:
                if (entered)
                    displayMatrix();
                else
                    printf("Please enter the graph first.\n");
                break;

            case 3:
                if (!entered)
                {
                    printf("Please enter the graph first.\n");
                    break;
                }

                printf("\nAvailable Locations:\n");

                for (i = 0; i < n; i++)
                    printf("%d. %s\n", i + 1, name[i]);

                printf("Select source location: ");
                scanf("%d", &source);

                if (source < 1 || source > n)
                {
                    printf("Invalid source location.\n");
                    source = 1;
                }

                source--;
                printf("Source selected: %s\n", name[source]);
                break;

            case 4:
                if (entered)
                    dijkstra(source, 0);
                else
                    printf("Please enter the graph first.\n");
                break;

            case 5:
                if (entered)
                    dijkstra(source, 1);
                else
                    printf("Please enter the graph first.\n");
                break;

            case 6:
                if (entered)
                    dijkstra(source, 0);
                else
                    printf("Please enter the graph first.\n");
                break;

            case 7:
                printf("Program terminated.\n");
                break;

            default:
                printf("Invalid choice. Try again.\n");
        }

    } while (choice != 7);

    return 0;
}
