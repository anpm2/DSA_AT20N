# BÀI TẬP LỚN
**MÔN HỌC: CẤU TRÚC DỮ LIỆU VÀ GIẢI THUẬT**

**CHỦ ĐỀ: Cây đỏ đen (Red-Black Tree)**

* Giảng viên: **ThS.Sử Nhật Hạ**
* Sinh viên: **Phạm Minh An**
* MSSV: **AT20N0102**
  
**Cài đặt thuật toán đầy đủ:**
* Đề bài:
![](https://github.com/anpm2/DSA_AT20N/blob/2ae2890a6983e9aa49e50560bc91164eebf9f49c/src/de.png)

* Kết quả:
![](https://github.com/anpm2/DSA_AT20N/blob/2ae2890a6983e9aa49e50560bc91164eebf9f49c/src/result.png)

* [source](https://github.com/anpm2/DSA_AT20N/blob/2ae2890a6983e9aa49e50560bc91164eebf9f49c/src/red-black-tree.cpp)
```cpp
// AT20N0102_PhamMinhAn_BTL_CTGL&GT

#include <iostream>
using namespace std;

// Màu của node
enum Color { 
    RED, BLACK 
};
struct Node {
    int data;
    Color color;
    Node* left;
    Node* right;
    Node* parent;

    Node(int data) {
        this->data = data;
        color = RED;
        left = right = parent = nullptr;
    }
};

class RedBlackTree {
private:
    Node* root;
    void leftRotate(Node* x) { // Quay trái
        Node* y = x->right;      // 1
		x->right = y->left;	     // 1
        if (y->left != nullptr) {
			y->left->parent = x; // 1
        }
		y->parent = x->parent;   // 1
        if (x->parent == nullptr) {
			root = y;		     // 1
        } 
        else if (x == x->parent->left) {
			x->parent->left = y; // 1
        } 
        else {
			x->parent->right = y;// 1
        }
		y->left = x;			 // 1
		x->parent = y;  	     // 1
	}// O(1+1+1+1+1+1+1+1) = O(8) = O(1)

    void rightRotate(Node* x) {   // Quay phải
		Node* y = x->left;	  // 1
		x->left = y->right;	  // 1
        if (y->right != nullptr) {
			y->right->parent = x; // 1
        }
		y->parent = x->parent;    // 1
        if (x->parent == nullptr) {
			root = y;			  // 1
        } 
        else if (x == x->parent->right) {
			x->parent->right = y; // 1
        } 
        else {
			x->parent->left = y;  // 1
        }
		y->right = x;			  // 1
		x->parent = y;			  // 1
	}// O(1+1+1+1+1+1+1+1) = O(8) = O(1)

    void fixInsert(Node* k) { // Sửa vi phạm sau khi thêm node
		while (k->parent != nullptr && k->parent->color == RED) { // lặp tối đa log(n) lần do chiều cao cây RB tối đa là log(n)
            if (k->parent == k->parent->parent->right) {
				Node* u = k->parent->parent->left;   // 1
                if (u != nullptr && u->color == RED) {
					u->color = BLACK;				 // 1
					k->parent->color = BLACK;	     // 1
					k->parent->parent->color = RED;  // 1
					k = k->parent->parent;		     // 1
                } 
                else {
                    if (k == k->parent->left) {
						k = k->parent;               // 1
						rightRotate(k);			     // O(1)
                    }
					k->parent->color = BLACK;		 // 1
					k->parent->parent->color = RED;  // 1
					leftRotate(k->parent->parent);   // O(1)
                }
            } 
            else {
				Node* u = k->parent->parent->right;  // 1

                if (u != nullptr && u->color == RED) {
					u->color = BLACK;				 // 1
					k->parent->color = BLACK;		 // 1
					k->parent->parent->color = RED;  // 1
					k = k->parent->parent;           // 1
                } 
                else {
                    if (k == k->parent->right) {
						k = k->parent;			     // 1
						leftRotate(k);			     // O(1)
                    }
					k->parent->color = BLACK;		 // 1
					k->parent->parent->color = RED;  // 1
					rightRotate(k->parent->parent);  // O(1)
                }
            }
            if (k == root) break;
        }
		root->color = BLACK;						 // 1
	}// O(log(n) * (1+1+1+1+1) = O(log(n) * 5) = O(5log(n)) = O(log(n))

    void insert(Node* new_node) {
        Node* parent = nullptr;                     // 1
		Node* current = root;					    // 1
		while (current != nullptr) {				// Lặp tối đa log(n) lần vì cây RB có chiều cao tối đa là log(n)
            parent = current;                       // 1
            if (new_node->data < current->data) {
				current = current->left; 		    // 1
            }
            else {
				current = current->right;		    // 1
            }
		}

		new_node->parent = parent;				    // 1

        if (parent == nullptr) {
			root = new_node;					    // 1
        }
        else if (new_node->data < parent->data) {
			parent->left = new_node;			    // 1
        }
        else {
			parent->right = new_node;			    // 1
        }

        if (new_node->parent == nullptr) {
			new_node->color = BLACK;				// 1
            return;
        }

        if (new_node->parent->parent == nullptr) {
            return;
        }
		fixInsert(new_node);						// O(log(n))
	}// O(1 + 1 + (1+1)*log(n) + 1 + 1 + log(n)) = O(4 + 3log(n)) = O(log(n))

    // Sửa vi phạm sau khi xóa node
    void fixdeleteNode(Node* x) {
		while (x != root && (x == nullptr || x->color == BLACK)) { // lặp tối đa log(n) lần do chiều cao cây RB tối đa là log(n)
            if (x == nullptr || x->parent == nullptr) 
                break;
            if (x == x->parent->left) {
			    Node* w = x->parent->right;             // 1
                if (w != nullptr && w->color == RED) {
				    w->color = BLACK;					// 1
				    x->parent->color = RED;				// 1
				    leftRotate(x->parent);				// O(1)
				    w = x->parent->right;				// 1
                }
                if ((w->left == nullptr || w->left->color == BLACK) && 
                    (w->right == nullptr || w->right->color == BLACK)) 
                {
				    if (w != nullptr) 
                        w->color = RED;	            // 1
				    x = x->parent;					// 1
                } 
                else 
                {
                    if (w->right == nullptr || w->right->color == BLACK) {
                        if (w->left != nullptr) 
						    w->left->color = BLACK;		// 1
                        if (w != nullptr) 
						    w->color = RED;				// 1
					    rightRotate(w); 			    // O(1)
					    w = x->parent->right;			// 1
                    }
                    if (w != nullptr) {
					    w->color = x->parent->color;	// 1
                        if (w->right != nullptr) 
						    w->right->color = BLACK;	// 1
                    }
				    x->parent->color = BLACK;			// 1
				    leftRotate(x->parent);				// O(1)
				    x = root;							// 1
                }
            } 
            else {
			    Node* w = x->parent->left;              // 1
                if (w != nullptr && w->color == RED) {
				    w->color = BLACK;					// 1
				    x->parent->color = RED;				// 1
				    rightRotate(x->parent);				// O(1)
				    w = x->parent->left;				// 1
                }
                if ((w->right == nullptr || w->right->color == BLACK) && 
                    (w->left == nullptr || w->left->color == BLACK)) 
                {
                    if (w != nullptr) 
					    w->color = RED;					// 1
				    x = x->parent;						// 1
                } 
                else {
                    if (w->left == nullptr || w->left->color == BLACK) {
                        if (w->right != nullptr) 
						    w->right->color = BLACK;	// 1
                        if (w != nullptr) 
						    w->color = RED;				// 1
					    leftRotate(w);					// O(1)
					    w = x->parent->left;			// 1
                    }
                    if (w != nullptr) {
					    w->color = x->parent->color;	// 1
                        if (w->left != nullptr) 
						    w->left->color = BLACK;		// 1
                    }
				    x->parent->color = BLACK;			// 1
				    rightRotate(x->parent);				// O(1)
				    x = root;							// 1
                }
            }
        }
        if (x != nullptr) {
    		x->color = BLACK; 						        // 1
        }
	}// O((1+1+1+1+1+1+1) * log(n)) = O(7log(n)) = O(log(n))

    // Hàm xoá node
    void deleteNode(Node* del) {
        Node* y = del;                                  // 1  
        Node* x;
        Color yOriginalColor = y->color;                // 1
        if (del->left == nullptr) {
            x = del->right;                             // 1
			replace(del, del->right);				    // O(1)
        } 
        else if (del->right == nullptr) {
			x = del->left;							    // 1
			replace(del, del->left);				    // O(1)
        } 
        else {
            y = del->left;                             // 1

            while (y->right != nullptr) // lặp tối đa O(log(n)) vì chiều cao cây RB tối đa là log(n)          
            {
				y = y->right;							// 1
            }
			yOriginalColor = y->color;				    // 1
			x = y->right;							    // 1
            if (y->parent == del) {
                if (x != nullptr) {
                    x->parent = y;                      // 1
                }
            } 
            else {
				replace(y, y->right);				    // O(1)
				y->right = del->right;				    // 1
				y->right->parent = y;				    // 1
            }
			replace(del, y);						    // O(1)
			y->left = del->left;					    // 1
			y->left->parent = y;					    // 1
			y->color = del->color;					    // 1
        }
        delete del;
        if (yOriginalColor == BLACK) {
			fixdeleteNode(x);						   // O(log(n))
        }
	}// O(1+1+1+1*log(n)+1+1+O(1)+1+1+O(1)+1+1+1+log(n)) = O(12+2log(n)) = O(log(n))

    // Thay thế node u bằng node v
    void replace(Node* u, Node* v) {
        if (u->parent == nullptr) {
			root = v;                                   // 1
        } 
        else if (u == u->parent->left) {
			u->parent->left = v;						// 1
        } 
        else {
			u->parent->right = v;						// 1
        }
        if (v != nullptr) {
			v->parent = u->parent;					    // 1
        }
	}// O(1+1) = O(2) = O(1)


    // Hàm tìm kiếm node
    Node* find(Node* node, int data) {
        if (node == nullptr || data == node->data) {
            return node;
        }
        if (data < node->data) {
			return find(node->left, data);              // O(log(n))
        }
		return find(node->right, data);				    // O(log(n))
	}// O(log(n) + log(n)) = O(2log(n)) = O(log(n))

    void showLNR(Node* node) { // Duyệt Left-Node-Right
        if (node != nullptr) {
            showLNR(node->left);
            cout << node->data << "(" << (node->color == RED ? "red" : "black") << ") ";
            showLNR(node->right);
        }
	}// O(n) vì duyệt qua tất cả các node

public:
    RedBlackTree() {
        root = nullptr;
    }

    void insert(int data) {
        Node* new_node = new Node(data);
        insert(new_node);
    }

    void deleteNode(int data) {
		Node* z = root;             // 1
		while (z != nullptr) {      // O(log(n))
            if (z->data == data) {
                cout << "\nDeleted: " << z->data << "(" << (z->color == RED ? "red" : "black") << ")" << endl;
                deleteNode(z);
                return;
            } 
            else if (z->data < data) {
                z = z->right;
            } 
            else {
                z = z->left;
            }
        }
        cout << "\nNot found!" << endl;
	}// O(1 + log(n)) = O(log(n))

    void showLNR() {
        cout << "\nLNR: ";
        showLNR(root);
        cout << endl;
	}// O(n)
    Node* find(int data) {
		return find(root, data);        // O(log(n))
    }
};

int main() {
    RedBlackTree rbt;
    int arr[] = {48, 95, 84, 40, 14, 71, 81, 53, 23, 33, 18, 86, 62 };
    for (int val : arr) {
        rbt.insert(val);
    }
    cout << "Red-Black Tree" << endl;
    rbt.showLNR();

    Node* x1 = rbt.find(23);
    if (x1 != nullptr) {
        cout << "\nFound: " << x1->data << "(" << (x1->color == RED ? "red" : "black") << ")" << endl;
    } 
    else {
        cout << "\nNot found!" << endl;
    }

    Node* x2 = rbt.find(71);
    if (x2 != nullptr) {
        cout << "\nFound: " << x2->data << "(" << (x2->color == RED ? "red" : "black") << ") " << endl;
    } 
    else {
        cout << "\nNot found!" << endl;
    }
    cout << endl;
    rbt.deleteNode(23);
    rbt.showLNR();
    cout << endl;

    rbt.deleteNode(71);
    rbt.showLNR();
    cout << endl;
    return 0;
}
```
