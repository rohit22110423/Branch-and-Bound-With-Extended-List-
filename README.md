import heapq
import networkx as nx
import matplotlib.pyplot as plt

def branch_and_bound(graph, start, goal):
    visited = set()
    distances = {node: float('inf') for node in graph}
    distances[start] = 0
    priority_queue = [(0, start, [])]

    while priority_queue:
        (cost, node, path) = heapq.heappop(priority_queue)

        if node in visited:
            continue

        path = path + [node]

        if node == goal:
            return path, cost

        visited.add(node)

        for neighbor, neighbor_cost in graph[node].items():
            if neighbor not in visited:
                new_cost = distances[node] + neighbor_cost
                if new_cost < distances[neighbor]:
                    distances[neighbor] = new_cost
                    heapq.heappush(priority_queue, (new_cost, neighbor, path))

    return None, float('inf')

graph = {
    'A': {'B': 2, 'C': 4, 'D': 1},
    'B': {'C': 3, 'E': 2},
    'C': {'D': 2, 'E': 5},
    'D': {'E': 3, 'F': 6},
    'E': {'F': 2, 'G': 1},
    'F': {'G': 3, 'H': 4},
    'G': {'I': 2},
    'H': {'I': 3, 'J': 5},
    'I': {'J': 1},
    'J': {}
}

start_node = 'A'
goal_node = 'J'

shortest_path, shortest_cost = branch_and_bound(graph, start_node, goal_node)

if shortest_path:
    print(f"Shortest Path from {start_node} to {goal_node}: {shortest_path}")
    print(f"Total Cost: {shortest_cost}")
    G = nx.Graph(graph)

    shortest_path_edges = [(shortest_path[i], shortest_path[i + 1]) for i in range(len(shortest_path) - 1)]

    edge_labels = {edge: graph[edge[0]][edge[1]] for edge in shortest_path_edges}

    fig, ax = plt.subplots()

    pos = nx.spring_layout(G)  
    nx.draw(G, pos, ax=ax, with_labels=True, node_size=500, node_color='lightblue')
    nx.draw_networkx_edge_labels(G, pos, edge_labels=edge_labels, font_color='red')
    nx.draw_networkx_edges(G, pos, edgelist=shortest_path_edges, edge_color='red', width=2)

    plt.show()

else:
    print(f"No path found from {start_node} to {goal_node}")

