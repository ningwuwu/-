#include<iostream>
#include<assert.h>
#include<string.h>
#include<assert.h>
//string类的模拟实现
namespace bit
{
	class String
{
public:
	typedef char* Iterator;
public:
	String(const char* str = " ")
	{
		if(str == nullptr)
		{
			assert(false);
			return ;
		}
		_size = strlen(str);
		_capacity = _size;
		_str = new char[strlen(str) + 1];
		strcpy(_str, str);
	}
	String(const String& s)
		: _str(new char[strlen(s._str) + 1])
		, _size(s._size)
		, _capacity(s._capacity)
		
	{
		strcpy(_str, s._str);
	}
	String& operator = (const String& s)
	{
		if(this != &s)
		{
			char* pstr = new char[strlen(s._str) + 1];
			strcpy(pstr, s._str);
			delete[] _str;
			_str = pstr;
			_size = s._size;
			_capacity = s._capacity;
		}
		return *this;
	}
	~String()
	{
		if(_str)
		{
			delete[] _str;
			_str = nullptr;
		}
	}
	//迭代器
	Iterator Begin()
	{
		return _str;
	}
	Iterator End()
	{
		return _str + _size;
	}
	void Reserve(size_t newCapacity)
	{
		if(newCapacity > _capacity)
		{
			char* str = new char[newCapacity + 1];
			strcpy(str, _str);
			delete[] _str;
			_str = str;
			_capacity = newCapacity;
		}
	}
	void PushBack(char c)
	{
		if(_size == _capacity)
			Reserve(_capacity * 2);
		_str[_size++] = c;
		_str[_size] = '\0';
	}
	void Append(size_t n, char c)
	{
		for(int i = 0; i < n; i++)
		{
			PushBack(c);
		}
	}
	String& operator += (char c)
	{
		PushBack(c);
		return *this;
	}
	bool operator<(const String& s)
	{
		return (&s < this);
	}
	bool operator<=(const String& s)
	{
		return !(&s > this);
	}
	bool operator>(const String& s)
	{
		return (&s > this);
	}
	bool operator>=(const String& s)
	{
		return !(&s < this);
	}
	bool operator==(const String& s)
	{
		return (&s == this);
	}
	bool operator!=(const String& s)
	{
		return !(&s == this);
	}
	//返回c在string中第一次出现的位置
	size_t Find (char c, size_t pos = 0) const
	{
		for(size_t i = pos; i < _size; i++)
		{
			if(c == _str[i])
				return i;
		}
		return 0;
	}
	size_t rFind (char c)
	{
		for(size_t i = _size; i >= 0; --i)
		{
			if(c == _str[i])
				return i;
		}
		return 0;
	}
	// 返回子串s在string中第一次出现的位置
	size_t Find (const char* s, size_t pos = 0) const
	{
		assert(s);
		size_t len = strlen(s);
		for(int i = pos; i < _size; i++)
		{
			int j = 0;
			if(_str[i] != s[j])
				continue;
			while(j < len && i + j < _size)
			{
				j++;
				if(_str[i+j] != s[j])
					break;
			}
			if(j == len)
				return i + 1;
		}
		return 0;
	}
 
        // 截取string从pos位置开始的n个字符
	String SubStr(size_t pos, size_t n)
	{
		if(pos > 0 && (pos <= _size))
		{
			char *tmp = new char[n];
			memcpy(tmp, _str + pos - 1, n - 1);
		}
		return *this;
	}
 
        // 在pos位置上插入字符c/字符串str，并返回该字符的位置
	String& Insert(size_t pos, const char* str)
	{
		//判断pos的值是否合理
		if(pos > 0 && pos <= _size)
		{
			//检查容量是否足够
			Reserve(strlen(str));
			//创建一块临时空间去接收_str中pos之后的数据
			char *tmp = new char[_size - pos + 2];
			strcpy(tmp, _str + pos -1);
			//此时需要将str的数据插入到pos之后，用strcat函数即可，但是注意被追加字符串的哪个字符串必须以'\0'结尾
			_str[pos - 1] = '\0';
			strcat(_str, str);
			strcat(_str, tmp);
			_size += strlen(str);
			_str[_size] = '\0';
			delete[] tmp;
		}
		return *this;
	}
 
        // 删除pos位置上的元素，并返回该元素的下一个位置
	String& Erase(size_t pos, size_t len)
	{
		if(pos > 0 && pos <= _size)
		{
			size_t npos = pos - 1;
			size_t index = npos + len;
			while(npos + len != _size)
			{
				_str[npos++] = _str[index++];
			}
			_size -= len;
			_str[_size] = '\0';
		}
		return *this;
	}

	void Append(const char* str)
	{
		size_t len = strlen(str);
		if(len > _capacity - _size)
			Reserve(2 * _capacity + len);
		/*for(int i = 0; i < strlen(str); i++)
		{
			PushBack(str[i]);
		}*/
		strcat(_str, str);
		_size += len;
	}
	void Resize(size_t newSize, char c = char())
	{
		if(newSize > _size)
		{
			//如果newSize大于旧_size，就需要开辟新空间
			Reserve(newSize);
			memset(_str + _size, c, newSize - _size);
		}
		_size = newSize;
		_str[_size] = '\0';
	}
	char& operator [](size_t index)
	{
		assert(index < _size);
		return _str[index];
	}
	const char& operator [](size_t index)const
	{
		assert(index < _size);
		return _str[index];
	}
private:
	friend ostream& operator << (ostream& _cout,const String& s);
private:
	char* _str;
	size_t _size;
	size_t _capacity;
};
	ostream& bit::operator << (ostream& _cout,const String& s)
	{
		cout << s._str;
		return _cout;
	}
}
int main()
{
	bit::String s1 = "wuqiongshigedashabi";
	bit::String(s2) = s1;
	char *str = "!!!";
	s1.Append(1,'!');
	s2.Append(str);
	cout << s1 << endl;
	cout << s2 << endl;
	bit::String s3 = "!!abcd!!ef!!!";
	cout << s3.Find("!!!", 1) << endl;
	bit::String SubStr();
	cout << s3.SubStr(3, 3) << endl;
	bit::String Insert();
	cout << s3.Insert(3, "???") << endl;
	bit::String Erase();
	cout << s3.Erase(3, 3) << endl;
	getchar();
	return 0;
}
