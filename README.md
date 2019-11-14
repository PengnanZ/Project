#include<algorithm>
using std::copy; using std::sort; using std::find; using std::set_intersection;
#include<cctype>
#include<fstream>
using std::ifstream; using std::ofstream;
#include<iostream>
using std::cout; using std::endl; using std::cin; using std::size_t;
#include <iomanip>
using std::fixed; using std::setprecision; using std::setiosflags;
#include<iterator>
using std::ostream_iterator; using std::back_inserter;
#include<map>
using std::map;
#include<numeric>
using std::accumulate;
#include <set>
using std::set;
#include<sstream>
using std::ostringstream; using std::istringstream;
#include<string>
using std::string;
#include<string.h>
#include<utility>
using std::pair;
#include<vector>
using std::vector;

int min(int a, int b, int c) { // find the min of 3 int
    if(a <= b && a <= c) {
        return a;
    } else if (b <= c) {
        return b;
    } else {
        return c;
    }
}

int Levenshtein(char a[], int a_len, char b[], int b_len) { // string could could be descript as a vector of char
    int m[a_len + 1][b_len + 1]; // create a matrix "m", m[i][j] which is the a[i] and b[j] character
    for (int i = 0; i <= a_len; i++) { // from a to b need delete
        m[i][0] = i;
    }
    for (int j = 1; j <= b_len; j++) { // for a to b need add
        m[0][j] = j;
    }
    for (int j = 0; j < b_len; j++) {
        for (int i = 0; i < a_len; i++) {
            if (a[i] == b[j]) { // do not need do anything become same
                m[i + 1][j + 1] = m[i][j];
            } else { // need to do any change to become same
                m[i + 1][j + 1] = min(m[i][j + 1] + 1, // need delete to become same
                                m[i + 1][j] + 1, // need add
                                m[i][j] + 1); // need other exchange
            }
        }
    }
    return m[a_len][b_len]; // the difference between two char[]
}

int the_most_simmilar(char a[], int a_length, vector<string> vec) {
    int num = 0;
    vector<int> result;
    for (size_t i = 0; i < vec.size(); i++) {
        string str = vec[i];
        int b_len = str.length();
        char c[b_len];
        strcpy(c, str.c_str());
        num += Levenshtein(a, a_length, c, b_len);
        result.push_back(num);
        num = 0;
    }
    sort(result.begin(),result.end());
    int output = result[0];
    return output;
    /*
    using the Levenshtein function to find the most similar password has how many different character
    */
}

int main() {
    ifstream in_file("common_passwords.txt");
    cout << "Give me a password:";
    string input;
    cin >> input;
    cout << endl;
    cout << "You provided a password of " << input << endl;
    // int diff = 0;
    int inplen = input.size();
    char a[inplen];
    strcpy(a, input.c_str()); // change string to char[]
    /* test use coding
    string trying = "fish"; 
    int plen = trying.length();
    char b[40];
    strcpy(b,trying.c_str());
    diff = Levenshtein(a, inplen, b, plen);
    cout << diff << "..........." << endl; // test use coding
    if (diff >= 0 && diff <= 5) {
        simmilar.push_back(trying);
    }
    */
    vector<string> commonpass;
    while (in_file) {
        string password;
        in_file >> password;
        if (password != " ") {
            commonpass.push_back(password);
        }
    }
    int min_diff = the_most_simmilar(a, inplen, commonpass);
    vector<string> simmilar;
    int chardiff = 0;
    for (size_t i = 0; i < commonpass.size(); i++) {
        string pass = commonpass[i];
        int pas_len = pass.size();
        char c[pas_len];
        strcpy(c, pass.c_str()); // change string to char[]
        chardiff = Levenshtein(a, inplen, c, pas_len);
        if (chardiff != min_diff) {
            chardiff = 0;
        } else {
            simmilar.push_back(pass);
            chardiff = 0;
        }
    }
    sort(simmilar.begin(),simmilar.end());
    cout << "The most similar passwords to " << input << " are:" << endl;
    vector<string>::iterator ite = simmilar.begin();
    for (; ite != simmilar.end(); ite++) {
        cout << *ite << ", ";
    }
    cout << endl;
    cout << "All of which are " << min_diff << " character(s) different." << endl;
    /* old trying code 
    map<int, vector<string>> data; // create a map which key is the length of a password, and value a vector with passwords
    int length = password.size();
        auto itr = data.find(length);
        if (itr != data.end()) { // if there is a same size password
            vector<string> renew;
            renew = data[length];
            renew.push_back(password);
            data.erase(length);
            data[length] = renew;
            renew.clear();
        } else { // if there is not same size password
            vector<string> paswdata;
            paswdata.push_back(password);
            data[length] = paswdata;
            paswdata.clear();
        }
    */
}
