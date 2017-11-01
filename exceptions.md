- **exceptions** : sofs17 exception definition module
	```cpp
	struct SOException:public std::exception
		int en;             ///< (system) error number
		const char *msg;    ///< name of function that has thrown the exception
	
