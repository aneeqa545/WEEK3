# WEEK3
#include <iostream>
#include <cstddef> // Include this header for nullptr

using namespace std;

// TreeNode class for Binary Search Tree (BST)
class TreeNode {
public:
    int data;
    TreeNode* left;
    TreeNode* right;

    TreeNode(int value) {
        data = value;
        left = NULL;
        right = NULL;
    }
};

// Heap class for Min-Heap
class MinHeap {
private:
    int* heap;
    int capacity;
    int size;

    void heapifyUp(int index) {
        if (index <= 0) return;
        int parent = (index - 1) / 2;
        if (heap[parent] > heap[index]) {
            swap(heap[parent], heap[index]);
            heapifyUp(parent);
        }
    }

    void heapifyDown(int index) {
        int left = 2 * index + 1;
        int right = 2 * index + 2;
        int smallest = index;

        if (left < size && heap[left] < heap[smallest])
            smallest = left;
        if (right < size && heap[right] < heap[smallest])
            smallest = right;

        if (smallest != index) {
            swap(heap[index], heap[smallest]);
            heapifyDown(smallest);
        }
    }

public:
    MinHeap(int* array, int n) {
        heap = new int[n];
        capacity = n;
        size = 0;
        for (int i = 0; i < n; ++i) {
            heap[size++] = array[i];
        }
        buildHeap();
    }

    ~MinHeap() {
        delete[] heap;
    }

    void insert(int value) {
        if (size >= capacity) {
            cout << "Heap capacity exceeded!" << endl;
            return;
        }
        heap[size++] = value;
        heapifyUp(size - 1);
    }

    void buildHeap() {
        for (int i = size / 2 - 1; i >= 0; --i) {
            heapifyDown(i);
        }
    }

    void display() {
        for (int i = 0; i < size; ++i) {
            cout << heap[i] << " ";
        }
        cout << endl;
    }

    int* getHeapArray() {
        return heap;
    }
};

// Function prototypes
TreeNode* populateTree(int* data, int n);
void add(TreeNode* node, int value);
void postOrder(TreeNode* node, int* result, int& index);
void preOrder(TreeNode* node, int* result, int& index);

// Function to populate BST from array data
TreeNode* populateTree(int* data, int n) {
    if (n == 0)
        return NULL;

    TreeNode* root = new TreeNode(data[0]);
    for (int i = 1; i < n; ++i) {
        add(root, data[i]);
    }
    return root;
}

// Function to add a node to BST
void add(TreeNode* node, int value) {
    if (value < node->data) {
        if (node->left != NULL)
            add(node->left, value);
        else
            node->left = new TreeNode(value);
    } else {
        if (node->right != NULL)
            add(node->right, value);
        else
            node->right = new TreeNode(value);
    }
}

// Function for post-order traversal of BST
void postOrder(TreeNode* node, int* result, int& index) {
    if (node == NULL)
        return;
    postOrder(node->left, result, index);
    postOrder(node->right, result, index);
    result[index++] = node->data;
}

// Function for pre-order traversal of BST
void preOrder(TreeNode* node, int* result, int& index) {
    if (node == NULL)
        return;
    result[index++] = node->data;
    preOrder(node->left, result, index);
    preOrder(node->right, result, index);
}

// Function to input data into array from user
int* inputData(int n) {
    int* data = new int[n];
    cout << "Enter " << n << " values to insert into BST: ";
    for (int i = 0; i < n; ++i) {
        cin >> data[i];
    }
    return data;
}

int main() {
    int n;
    cout << "How many nodes do you want to insert into BST: ";
    cin >> n;
    
    int* data = inputData(n);

    // Step 1: Create BST
    TreeNode* root = populateTree(data, n);

    // Step 2: Traverse BST using post-order and build Min-Heap using insert method
    int postOrderResult[n];
    int postIndex = 0;
    postOrder(root, postOrderResult, postIndex);
    cout << "Post-order traversal of BST: ";
    for (int i = 0; i < n; ++i) {
        cout << postOrderResult[i] << " ";
    }
    cout << endl;

    MinHeap heap1(postOrderResult, n);
    cout << "Min-Heap using insert method: ";
    heap1.display();

    // Step 4: Traverse BST using pre-order and build Min-Heap using buildHeap method
    int preOrderResult[n];
    int preIndex = 0;
    preOrder(root, preOrderResult, preIndex);
    cout << "Pre-order traversal of BST: ";
    for (int i = 0; i < n; ++i) {
        cout << preOrderResult[i] << " ";
    }
    cout << endl;

    int* heapArray = heap1.getHeapArray();
    MinHeap heap2(heapArray, n);
    cout << "Min-Heap using buildHeap method: ";
    heap2.display();

    // Clean up memory for BST nodes (optional if no longer needed)
    // Code for freeing BST nodes can be added here
    delete[] data;

    return 0;
}
