
using namespace std;

// Structure to represent a blog post
struct Post {
    string title;
    string author;
    string content;
    string timestamp;
};

// Function to get current timestamp
string getCurrentTimestamp() {
    time_t now = time(0);
    char buffer[80];
    strftime(buffer, 80, "%Y-%m-%dT%H:%M:%SZ", localtime(&now));
    return string(buffer);
}

// Function to create a new post
Post createPost() {
    Post post;
    cout << "Enter title: ";
    getline(cin, post.title);
    cout << "Enter author: ";
    getline(cin, post.author);
    cout << "Enter content: ";
    getline(cin, post.content);
    post.timestamp = getCurrentTimestamp();
    return post;
}

// Function to save a post to a file
void savePost(const Post& post, const string& filename) {
    ofstream outfile(filename, ios::app);
    outfile << "---" << endl;
    outfile << "title: " << post.title << endl;
    outfile << "author: " << post.author << endl;
    outfile << "content: " << post.content << endl;
    outfile << "timestamp: " << post.timestamp << endl;
    outfile << "---" << endl << endl;
    outfile.close();
}

// Function to load posts from a file
vector<Post> loadPosts(const string& filename) {
    vector<Post> posts;
    ifstream infile(filename);
    string line;

    Post currentPost;
    bool readingPost = false;

    while (getline(infile, line)) {
        if (line == "---") {
            if (readingPost) {
                posts.push_back(currentPost);
                currentPost = Post();
            }
            readingPost = !readingPost;
        } else if (readingPost) {
            stringstream ss(line);
            string key, value;
            getline(ss, key, ':');
            getline(ss, value);

            if (key == "title") {
                currentPost.title = value;
            } else if (key == "author") {
                currentPost.author = value;
            } else if (key == "content") {
                currentPost.content = value;
            } else if (key == "timestamp") {
                currentPost.timestamp = value;
            }
        }
    }

    // Add the last post if it was being read
    if (readingPost) {
        posts.push_back(currentPost);
    }

    infile.close();
    return posts;
}

// Function to display a post
void displayPost(const Post& post) {
    cout << "Title: " << post.title << endl;
    cout << "Author: " << post.author << endl;
    cout << "Content: " << post.content << endl;
    cout << "Timestamp: " << post.timestamp << endl << endl;
}

int main() {
    // File to store posts
    const string filename = "posts.txt";

    // Load existing posts
    vector<Post> posts = loadPosts(filename);

    // Main loop
    while (true) {
        cout << "Choose an option:" << endl;
        cout << "1. Create new post" << endl;
        cout << "2. Display posts" << endl;
        cout << "3. Exit" << endl;

        int choice;
        cin >> choice;
        cin.ignore();

        if (choice == 1) {
            // Create a new post
            Post newPost = createPost();
            // Save the post
            savePost(newPost, filename);
            cout << "Post created successfully!" << endl << endl;
        } else if (choice == 2) {
            // Display posts
            if (posts.empty()) {
                cout << "No posts available." << endl << endl;
            } else {
                for (const Post& post : posts) {
                    displayPost(post);
                }
            }
        } else if (choice == 3) {
            break;
        } else {
            cout << "Invalid choice. Please try again." << endl << endl;
        }
    }

    return 0;
}
