1. Lazizbek, Kahramonov
2. Contact Info:
   * email address
lazizbekexclusive@gmail.com
   * telegram username:
   @algorithmer
3. **Summary**
**My goal** is to gain enough skills and knowledge in the field of programming so that I can create something beneficial for my society. 
Right now, mastering a particular programming language and working with it at a professional level is **very important**.
4. As of now, I have intermediate skills of C++  and basics of HTML5 and CSS.
5.  This a project I created for the final exam of Data Structures course.

```c++
{
  // Lazizbek Kahramonov assigment Data  Structure

#include<iostream>
#include<string>
using namespace std;

enum FlightClass{ first, econ };//initializing flight class types as enum variable

typedef struct NODE{//declaration of node structure

	string id, name;
	FlightClass Class;
	int seat{};


	struct NODE *prev;
	struct NODE *next;
}Node;

Node *firstHead = NULL;
Node *econHead = NULL;
//initial free seat numbers 
int freeFirst[5]{1, 2, 3, 4, 5};
int freeEcon[15]{6, 7, 8, 9, 10, 11, 12, 13, 14, 15, 16, 17, 18, 19, 20};

//function prototypes
void insert(Node **head, string id, string name, FlightClass Class, int seat);
Node *getLast(Node *head);
void displayList(Node *head);
void displayNode(Node *head);
void setDetails(Node **head, Node *head2, FlightClass Class);
bool verifyID(Node *head, string id);
int countSeats(Node *head);
void handleFirst(Node **firstHead);
void handleEcon(Node **econHead);
void deAllocateHeap(Node **head);
void handleCancel(Node ** firstHead, Node **econHead);
void moveFreeSeats(int *seat, Node *head);
void addCancelled(int *freeSeats, int cancelled);
void handleAdmin(Node *firstHead, Node *econHead);

void insert(Node **head, string id, string name, FlightClass Class, int seat){
	Node *newNode = new Node;

	newNode->id = id;
	newNode->name = name;
	newNode->Class = Class;
	newNode->seat = seat;

	newNode->next = NULL;
	if ((*head) == NULL){//initially head is NULL
		newNode->prev = NULL;
		(*head) = newNode;//assign head as newnode
	}
	else{//if list is not empty
		Node *last = getLast(*head);
		last->next = newNode;
		newNode->prev = last;
	}
	displayNode(newNode);
}

Node *getLast(Node *head){//returns the last node
	Node *last = head;
	while (last->next){
		last = last->next;
	}
	return last;
}
void displayList(Node* head){//displays the full list (all the passenger details)

	while (head != NULL){
		displayNode(head);
		head = head->next;
	}
	if (head == NULL)
		cout << " ";
}
void displayNode(Node *head){//displays ine node(one passenger only)
	cout << "\n********Boarding Pass********" << endl;
	cout << "Passport ID  : " << head->id << endl;
	cout << "Name         : " << head->name << endl;

	if (head->Class == first)
		cout << "Flight Class : First Class" << endl;
	else if (head->Class == econ)
		cout << "Flight Class : Economy Class" << endl;
	cout << "Seat Number  : " << head->seat << endl;
}
void setDetails(Node **head, Node *head2, FlightClass Class){// head2 is needed to verify ID
	string id, name;
	cout << "\nEnter Passport ID : ";
	getline(cin, id);

	if (verifyID(*head, id)||verifyID(head2, id)){//check if ID is not repeated 
		cout << "Repeated ID !\nYou have already reserved a ticket !" << endl;
		return;
	}//otherwise proceed
	cout << "\nEnter Name        : ";
	getline(cin, name);
	
	if (id == "" || name == ""){
		cout << "\nPlease enter valid infromation !" << endl;
		setDetails(head, head2, Class);
	}
	else{
		if (Class == first){
			insert(head, id, name, Class, freeFirst[0]);//create node//give seat number from array of seats
			moveFreeSeats(freeFirst, *head);//give seat number from array and move array elements to left
		}
		else if (Class == econ){
			insert(head, id, name, Class, freeEcon[0]);
			moveFreeSeats(freeEcon, *head);
		}
	}
}
void moveFreeSeats(int *seat, Node *head){
	int numSeats;
	if (head->Class == first)
		numSeats = 5;//there are 5 seats for 1st class
	else if (head->Class == econ)
		numSeats = 15;//there are 15 seats for economy class
	for (int i{}; i < numSeats; i++){
		seat[i] = seat[i + 1];//move to left by 1 elements
	}
	seat[numSeats - 1] = 0;//set last (occupied) element to 0
}
bool verifyID(Node *head, string id){
	
	while (head != NULL){//loop through the list and check the IDs
		if (head->id == id)
			return true;
		head = head->next;
	}
	return false;
}
int countSeats(Node *head){
	int i{};
	while (head != NULL){//loop through the list and count nodes
		++i;
		head = head->next;
	}
	return i;
}

void handleFirst(Node **firstHead){
	FlightClass Class;
	if (countSeats(*firstHead) == 5){//count nodes to check if all seats are taken
		string economyClass;
		cout << "\nWe are sorry but First Class is already full...\nYou can try Economy " << endl;
		cout << "Would you like ?\n Enter y/n :";//give recommendation of a seat in economy class because first class is full
		getline(cin, economyClass);
		if (economyClass == "y")
			handleEcon(&econHead);//go to economy registration
	}
	else{//otherwise start registration 
		Class = first;
		cout << "\n*****Registration for FIRST CLASS*****" << endl;
		setDetails(firstHead, econHead, first);
	}
}




void handleEcon(Node **econHead){
	FlightClass Class;
	if (countSeats(*econHead) == 15){//count nodes to check if all seats are taken
		string firstClass;
		cout << "\nWe are sorry but Economy is already full...\nYou can try First Class " << endl;
		cout << "Would you like ?\n Enter y/n :";//give recommendation of a seat in first class because economy class is full
		getline(cin, firstClass);
		if (firstClass == "y")
			handleFirst(&firstHead);//go to frst class registration
	}
	else{
		Class = econ;
		cout << "\n*****Registration for ECONOMY*****" << endl;
		setDetails(econHead, firstHead, econ);
	}
}


Node *getID(Node *firstHead, Node *econHead, string id){
	Node *temp;
	while (firstHead != NULL){//loop through the first class and check the IDs
		if (firstHead->id == id)
			return firstHead;
		firstHead = firstHead->next;
	}
	while (econHead != NULL){//loop through the economy and check the IDs
		if (econHead->id == id)
			return econHead;
		econHead = econHead->next;
	}
	return NULL;
}
void deleteNode(Node **head, Node *del){
	if (*head == NULL || del == NULL)
		return;
	if (*head == del)//id we want to delete head node then move head first
		*head = del->next;
	if (del->next != NULL)//change next only when the node to be deleted is NOT the last node 
		del->next->prev = del->prev;
	if (del->prev != NULL)//change prev only when the node to be deleted is NOT the first node 
		del->prev->next = del->next;

	delete del;//free the allocated heap
	return;
}
void handleCancel(Node **firstHead, Node **econHead){
	cout << "Enter your ID : ";
	string ID;
	getline(cin, ID);
	
	Node *del=getID(*firstHead, *econHead, ID);//get the node which is to be deleted

	if (del->Class == first){//check whether first class
		cout << "\nYour cancellation of First Class seat number "<< del->seat<<" is successfull..." << endl;
		addCancelled(freeFirst, del->seat);//add the cancelled seat to the list of available seats again
		deleteNode(firstHead, del);
	}
	else if (del->Class == econ){//check whether econ class
		cout << "\nYour cancellation of Economy Class seat number " << del->seat << " is successfull..." << endl;
		addCancelled(freeEcon, del->seat);//add the cancelled seat to the list of available seats again
		deleteNode(econHead, del);
	}
}
void handleAdmin(Node *firstHead, Node *econHead){
	cout << "\n*************Details of all passengers***********" << endl;
	cout << "\n*************FIRST CLASS***********" << endl;
	displayList(firstHead);
	cout << endl;
	cout << "\n*************ECONOMY CLASS*********" << endl;
	displayList(econHead);
}
void addCancelled(int *freeSeats, int cancelled){//adds cancelled seat numbers to the list of available seats again
	int i{};
	while (freeSeats[i] != 0){
		i++;
	}
	freeSeats[i] = cancelled;
}

bool noSeats(Node *firstHead, Node *econHead){
	if ((countSeats(econHead) == 15) && (countSeats(firstHead) == 5)){
		cout << "There are no seats !\nNext flight leaves in 2 hours..." << endl;
		return true;
	}
	return false;
}

void deAllocateHeap(Node **head){//deallocate heap
	Node *temp;
	while (*head != NULL){
		temp = *head;
		*head = (*head)->next;
		delete temp;
	}
}


int main(){
	string input{};
	do{
		cout << "\n\t\t\tPlease type 1 for \"first class\"" << endl;
		cout << "\t\t\tPlease type 2 for \"economy\"" << endl;
		cout << "\t\t\tPlease type 3 to cancel reservation" << endl;
		cout << "\t\t\tPLease type 0 to quit the program" << endl;
		cout << "\t\t\tPlease type the password if you are admin\n" << endl;//password = 12345678 (the most complicated password in the world)
		cout << "\t\t\tEnter your choice : ";

		getline(cin, input);

		if (input == "1"){
			if (noSeats(firstHead, econHead))
				break;
			else
				handleFirst(&firstHead);
		}
		else if (input == "2"){
			if (noSeats(firstHead, econHead))
				break;
			else
				handleEcon(&econHead);
		}
		else if (input == "3")
			handleCancel(&firstHead, &econHead);
		else if (input == "0")
			cout << "\nBye... Thank you for using our service. \nPlease share, like and subscribe to support us !" << endl;
		else if (input == "12345678")
			handleAdmin(firstHead, econHead);//shows all the passenger details
		else 
			cout << "\nInvalid input" << endl;
	} while (input != "0");

	deAllocateHeap(&firstHead);
	deAllocateHeap(&econHead);
	
	cout << endl;
	system("pause");
	return 0;
}



}

```
6. I had quite a few project at the University. Here are some:
   * Console application for reserving and cancelling a flight ticket
   * Console application to store and manage a list of students in a file
7. Mainly, I completed a C++ course at Udemy.com
8. I have been studying at a university where all the courses are taught in English. So, I do not have any difficulty understanding or utilizing the English language.