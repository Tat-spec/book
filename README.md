# book// SPDX-License-Identifier: MIT
pragma solidity ^0.8.0;

contract BookLibrary {

    struct Book {
        string title;
        string author;
        bool isBorrowed;
        address borrowedBy;
    }

    Book[] public books;
    mapping(uint256 => address) public bookToBorrower;

    event BookAdded(uint256 bookId, string title, string author);
    event BookBorrowed(uint256 bookId, address borrower);
    event BookReturned(uint256 bookId, address borrower);

    // Add a new book to the library
    function addBook(string memory _title, string memory _author) public {
        books.push(Book({
            title: _title,
            author: _author,
            isBorrowed: false,
            borrowedBy: address(0)
        }));
        uint256 bookId = books.length - 1;
        emit BookAdded(bookId, _title, _author);
    }

    // Borrow a book by its ID
    function borrowBook(uint256 _bookId) public {
        require(_bookId < books.length, "Invalid book ID");
        Book storage book = books[_bookId];
        require(!book.isBorrowed, "Book already borrowed");

        book.isBorrowed = true;
        book.borrowedBy = msg.sender;
        bookToBorrower[_bookId] = msg.sender;

        emit BookBorrowed(_bookId, msg.sender);
    }

    // Return a borrowed book
    function returnBook(uint256 _bookId) public {
        require(_bookId < books.length, "Invalid book ID");
        Book storage book = books[_bookId];
        require(book.isBorrowed, "Book is not borrowed");
        require(book.borrowedBy == msg.sender, "You did not borrow this book");

        book.isBorrowed = false;
        book.borrowedBy = address(0);
        delete bookToBorrower[_bookId];

        emit BookReturned(_bookId, msg.sender);
    }

    // Get the details of a book by its ID
    function getBook(uint256 _bookId) public view returns (string memory title, string memory author, bool isBorrowed, address borrowedBy) {
        require(_bookId < books.length, "Invalid book ID");
        Book memory book = books[_bookId];
        return (book.title, book.author, book.isBorrowed, book.borrowedBy);
    }

    // Get the total number of books in the library
    function getTotalBooks() public view returns (uint256) {
        return books.length;
    }
}
