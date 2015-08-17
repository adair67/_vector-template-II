_vector-template-II
===================


// testgd_template.cpp : 定义控制台应用程序的入口点。
//
/*
 * Module Name: _vector-template-II
 * Version: 0.1
 * Author: qi han (eric)
 * License : BSD 
 * Description: _vector template class
 */

#include "stdafx.h"

#include <windows.h>
#include <winbase.h>

#include <iostream>
#include <vector>
#include <list>
#include <queue>
#include <deque>
using namespace std;

// For my baby daughter  Ruize Han , she achieve her dream 

template <typename TYPE>
struct _item    //指针链表
{
public:
	struct _item * _next, * _pre;
	TYPE* _data;
	int	_count;
};


template <typename TYPE>
struct _vector
{
protected:
	_item<TYPE> *_pitem,*_ptailitem;
	int	_size;
	int	_capacity;
	int	_step;

public:
	struct iterator
	{
		_item<TYPE> *_pp ,*_pptmp;
		int _index;

		iterator()	{ _pp = NULL; _index =0; }
		iterator(_item<TYPE> * pp,int index)	{ _pp = pp; _index = index; }
		virtual ~iterator()	{ _pp = NULL; _index =0; }

		virtual void	operator + (int forward) 
		{
			while (NULL != _pp && forward > 0)
			{
				if (_index + forward < _pp->_count)
				{
					_index += forward;
					forward = 0;
				}
				else
				{
					forward -= _pp->_count - _index -1;
					_pptmp = _pp; //
					_pp = _pp->_next;
					_index = -1;
				
				}
			}

			if(NULL == _pp)
			{
				//_pp = _pptmp;
				//_index = _pp->_count - 1;
				_index = 0;
			}

		}

		virtual void	operator += (int forward)	{ operator + (forward); }
		virtual void	operator ++ ()	{ operator + (1); }
		virtual void	operator ++(int)	{ operator + (1) ; }
		virtual bool	operator != (iterator& iter)	{ return !(_pp == iter._pp && _index == iter._index); }
		virtual TYPE&	operator * ()	{ return *(_pp->_data + _index); }

	};

public:
	_vector(int step=50) { _pitem = NULL; _size = 0; _capacity = 0; _step = step; _ptailitem = NULL; }  //调整step值
	virtual ~_vector() { clear(_pitem);	_pitem = NULL; _size = 0; _capacity = 0; _ptailitem = NULL; }

	iterator	vbegin()	{ return iterator(_pitem,0); }
	iterator	vend()		{ return iterator(_ptailitem!=NULL?_ptailitem->_next:_ptailitem,0); }
/*	
	iterator	vend()		
	{
		_item<TYPE> * _pitemtmp = _ptailitem==NULL?_ptailitem->_pre:_ptailitem;
		int index = _ptailitem==NULL?_ptailitem->_pre->_count -1:_ptailitem->_count - 1;
		return iterator(_pitemtmp, index); 
	}
*/	
	virtual void	push_front(const TYPE& p) { push(0, p); }
	virtual void	push_back(const TYPE& p)  { push(size(), p); }
	virtual TYPE	pop_front()			{ return pop(0); }
	virtual TYPE	pop_back()			{ return pop(size()-1); }

	int		begin()	{ return 0; }
	int		end()	{ return size(); }

	TYPE&	front()		{ return value(0); }	
	TYPE&	back()		{ return value(size()-1); }	

	virtual void	insert(int where, const TYPE& p)  { push(where, p); }
	virtual TYPE	erase(int where)            { return pop(where); }
	
	int  size()     { return _size; }	const
	int  capacity() { return _capacity; } 
	bool empty()    {return _size == 0;}

	virtual	void	assign(int total,const TYPE& p)	{ resize(total);  iterator it;  for (it= vbegin() ; it != vend() ; it++)  *it = p; }
	virtual void    resize(int);

	virtual TYPE&	operator[] (int index) { return value(index); }	
	virtual TYPE&	value(int) ;

	virtual void clear()  { clear(_pitem); _pitem = NULL; _size = 0; _capacity = 0; _ptailitem = NULL; }

private:
	void	push(int, const TYPE&);
	TYPE	pop(int);

	void clear(_item<TYPE> * pitem) 
	{
		_item<TYPE> * ptemp;

		while(NULL != pitem)
		{
			delete[] pitem->_data;         //释放
			_capacity -= _step;
			_size -= pitem->_count;
			ptemp = pitem->_next;
			delete pitem;
			pitem = ptemp;
		}
	}

	int seprator1(int i) { return i * 382 / 1000 ;}
	int seprator2(int i) { return i * 618 / 1000 ;}

};



template <typename TYPE>
void _vector<TYPE>::resize(int new_size)
{
	 _item<TYPE> * ptemp, * pptemp,* pp;
	 int count = 0;

	 if (new_size < size())
	 {
			ptemp = _pitem;
			pptemp = NULL;
	 		
	 		while(NULL != ptemp)
	 		{
				count += ptemp->_count;
		
				if (new_size <= count)
				{
					break;
				}
				else if (new_size > count)
				{
					pptemp = ptemp;
					ptemp = ptemp->_next;
				}
	 		}
	 		
	 		ptemp->_count = new_size - (count - ptemp->_count);

			if (ptemp->_count == 0)
			{
				pp = ptemp;

				if (NULL != pptemp)	
				{
					pptemp->_next = NULL;
				}
				else
				{
					_pitem = NULL;

				}
				_ptailitem = pptemp;
			}
			else
			{
				pp = ptemp->_next;
				ptemp->_next = NULL;
				_ptailitem = ptemp;
			}
	 		
	 		clear(pp);

	 		_size = new_size;
	 		
	 }
	 else if (new_size > size())
	 {
	 	ptemp = _ptailitem;
	 	pptemp = NULL;

		if (NULL != ptemp && new_size - size() + ptemp->_count <= _step)
		{
			ptemp->_count += new_size - size();
			_size = new_size;
		}
		else
		{
	 		count = size();

	 		while(count + _step <= new_size)
	 		{
	 			pp = new  _item<TYPE>();
				pp->_data = new  TYPE[_step]; 
				pp->_next = NULL;
				pp->_pre = ptemp;
				pp->_count = _step;

				if (NULL != ptemp) 
				{
					ptemp->_next = pp;
				}
				else
				{
					_pitem = pp;
				}
				
				_capacity += _step;
				_size += _step;

				count += _step;
				
				pptemp = ptemp;
				if (NULL != ptemp) 
				{
					ptemp = ptemp->_next;
				}
				else
				{
					ptemp = _pitem;
				}
				
			}
			
			if (count < new_size)
			{
	 			pp = new  _item<TYPE>();
				pp->_data = new  TYPE[_step]; 
				pp->_next = NULL;
				pp->_pre = ptemp;
				pp->_count = new_size-count;

				if (NULL != ptemp) 
				{
					ptemp->_next = pp;
				}
				else
				{
					_pitem = pp;
				}
				
				_capacity += _step;
				_size += new_size-count;

				count += new_size-count;
				
				pptemp = ptemp;
				if (NULL != ptemp) 
				{
					ptemp = ptemp->_next;
				}
				else
				{
					ptemp = _pitem;
				}
			}
			
			_ptailitem = ptemp;
		}
	 }
}


template <typename TYPE>
TYPE _vector<TYPE>::pop(int where)
{
	if (where < 0 || where >= size())   ///索引越界
	{
		//return 0;
	}	
	
	_item<TYPE> * ptemp, * pptemp;
	int count = 0;
	int offset;
	TYPE p;

	ptemp = _pitem;
	pptemp = NULL;

	if (where == size()-1)
	{
		count = size();
		ptemp = _ptailitem;
	}
	else
	{
	  while(NULL != ptemp)
	  {
			count += ptemp->_count;
		
			if (where+1 <= count)
			{
				break;
			}
			else if (where+1 > count)
			{
				pptemp = ptemp;
				ptemp = ptemp->_next;
			}
	  }
	}
	
	p = *( ptemp->_data + (where+1) - (count - ptemp->_count+1) );

	for(offset = (where+1) - (count - ptemp->_count+1) +1 ;  offset <= ptemp->_count-1 ; ++offset)
	{
		*(ptemp->_data+offset-1) = *(ptemp->_data+offset);
	}


	ptemp->_count -= 1;

	if (ptemp->_count == 0 )  //本item变空须删除释放
	{
		if (NULL != ptemp->_pre)
		{
			ptemp->_pre->_next = ptemp->_next;
		}
		else
		{
			_pitem = ptemp->_next;   //最初一项
		}
		
		if (NULL != ptemp->_next)
		{
			ptemp->_next->_pre = ptemp->_pre;
		}
		else
		if (where == size()-1)      //最后一项
		{
			//_ptailitem = pptemp;   
			_ptailitem = ptemp->_pre;
		}
		
		_capacity -= _step;
		delete[] ptemp->_data;
		delete ptemp;
	}

	_size -= 1;	
	
	return p;
}



template <typename TYPE>
TYPE& _vector<TYPE>::value (int where) 
{
	if (where < 0 || where >= size())   ///索引越界
	{
		//return NULL;
	}
	
	_item<TYPE> * ptemp,* pptemp;
	int count = 0;

	ptemp = _pitem;
	pptemp = NULL;

	if (where == size()-1)
	{
		count = size();
		ptemp = _ptailitem;
	}
	else
	{
	  while(NULL != ptemp)
	  {
			count += ptemp->_count;
		
			if (where+1 <= count)
			{
				break;
			}
			else if (where+1 > count)
			{
				pptemp = ptemp;
				ptemp = ptemp->_next;
			}
	  }
	}
	
	return *(ptemp->_data + (where+1) - (count - ptemp->_count +1));
}



template <typename TYPE>
void _vector<TYPE>::push(int where, const TYPE& p)
{
	if (where < 0 || where > size()) 
	{
		return ;
	}
	
	_item<TYPE> * ptemp,* pptemp,* pp;
	int count = 0;
	int offset;
	
	ptemp = _pitem;
	pptemp = NULL;


	if (where == size())
	{
		count = size();

		pptemp = _ptailitem;
		ptemp = NULL;
	}
	else
	{
		while(NULL != ptemp)
		{
			count += ptemp->_count;
		
			if (where+1 <= count)
			{
				break;
			}
			else if (where+1 > count)
			{
				pptemp = ptemp;
				ptemp = ptemp->_next;
			}
		}
	}

	
	bool inserted = false;
	int item_count;
	
	if (NULL != ptemp )
	{
		item_count = ptemp->_count;
	}
	else
	{
		item_count = 0;
	}
	
	if (where+1 == count - item_count + 1 )    //item的第一个元素位置,
	{
		if (!inserted && NULL != pptemp && pptemp->_count < _step)    //前面一个item未满
		{
			*(pptemp->_data+pptemp->_count) = p;
			pptemp->_count +=1;
			_size += 1;	
			
			inserted = true;
		}
		
		if (!inserted && NULL != ptemp && ptemp->_count <= seprator2(_step))       //item 未满一半, 向后挪数据
		{
			for(offset = ptemp->_count-1 ;  offset>=(where+1) - (count - ptemp->_count+1) ; --offset)
			{
				*(ptemp->_data+offset+1) = *(ptemp->_data+offset);
			}
			
			ptemp->_count +=1;
			*(ptemp->_data+offset+1) = p;
			_size += 1;	
			
			inserted = true;
		}
		
		if (!inserted &&       
			  ((NULL != ptemp && ptemp->_count > seprator2(_step)) ||  ///item已满一半，不需向后挪数据，前面插入一个item
			   (NULL == ptemp)))
		{
			pp = new  _item<TYPE>();
			pp->_data = new  TYPE[_step]; 
			pp->_next = ptemp;
			pp->_pre = pptemp;
			
			if (NULL != pp->_next)
			{
			  pp->_next->_pre = pp;
			}
			
			if (NULL != pp->_pre)
			{
				pp->_pre->_next = pp;                    //处理一种push_back情况
			}
			else
			{
				_pitem = pp;
			}

			if (NULL == pp->_next) 
			{
				_ptailitem = pp;
			}

			_capacity += _step;
			*(pp->_data) = p;
			pp->_count = 1;
			_size += 1;	
			
			inserted = true;
			
		}
	}
	else                               //item的非第一个元素位置,向后挪数据
	{
		if (!inserted && NULL != ptemp && ptemp->_count < _step)                            //item 未满  （push_back情况）
		{
			for(offset=ptemp->_count-1 ;  offset>=(where+1) - (count - ptemp->_count+1) ; --offset)
			{
				*(ptemp->_data+offset+1) = *(ptemp->_data+offset);
			}
			
			*(ptemp->_data+offset+1) = p;
			ptemp->_count +=1;
			_size += 1;	
			
			inserted = true;
			
		}
		
		if(!inserted && NULL != ptemp->_next && ptemp->_next->_count <= seprator1(_step))           //后面item未满一半, 向后面item挪一个数据
		{
			for(offset=ptemp->_next->_count-1 ;  offset>=0 ; --offset)
			{
				*(ptemp->_next->_data+offset+1) = *(ptemp->_next->_data+offset);
			}
			
			*(ptemp->_next->_data+offset+1) = *(ptemp->_data+_step-1);   //向后面item挪一个数据
			ptemp->_next->_count +=1;

			ptemp->_count -=1;
			
			for(offset=ptemp->_count-1 ;  offset>=(where+1) - (count -1 - ptemp->_count+1) ; --offset)
			{
				*(ptemp->_data+offset+1) = *(ptemp->_data+offset);
			}
			
			*(ptemp->_data+offset+1) = p;
			ptemp->_count +=1;
			_size += 1;	
			
			inserted = true;
		}
		
		if(!inserted && 
		   ((NULL != ptemp->_next && ptemp->_next->_count > seprator1(_step))  ||             //后面item已满一半, 后面插入一个item
			(NULL == ptemp->_next)))
		{
				pp = new  _item<TYPE>();
				pp->_data = new  TYPE[_step]; 
				pp->_next = ptemp->_next;
				pp->_pre = ptemp;
				
				pp->_pre->_next = pp; 

				if (NULL != pp->_next) 
				{
					pp->_next->_pre = pp;
				}
				else
				if (NULL == pp->_next) 
				{
					_ptailitem = pp;
				}

				
				_capacity += _step;
				pp->_count = 1;
				*(pp->_data) = *(ptemp->_data+_step-1);              //向后面item挪一个数据
	
				ptemp->_count -= 1;
				
				for(offset=ptemp->_count-1 ;  offset>=(where+1) - (count -1 - ptemp->_count+1) ; --offset)
				{
					*(ptemp->_data+offset+1) = *(ptemp->_data+offset);
				}
			
				*(ptemp->_data+offset+1) = p;
				ptemp->_count += 1;
				_size += 1;	
				
				inserted = true;
		}
		
	}
}





template <typename TYPE>
struct _queue:public _vector<TYPE>
{
public:
	_queue() { } 
	_queue(const _queue<TYPE>&){ }
	virtual ~_queue(){ }

	virtual void push(const TYPE& p)  { push_back(p) ; }
	virtual	TYPE pop()  { return pop_front(); }

	const TYPE&	front()		{ return value(0); }	
	const TYPE&	back()		{ return value(size()-1); }	

	int  size()     { return _size;; }	
	bool empty()    { return _size==0; }	
};




typedef  void*  PVOID;



struct xyzrhw_uv_t
{
	double	xyzrhw;
	double	uv;
};


class A
{
public:
	A(){ }
	A(const A&){ }
	virtual ~A(){ }

	void print(){ cout<<"i am A"<<endl<<endl<<endl; }

};



int _tmain(int argc, _TCHAR* argv[])
{
	vector<void*>	pvector;
	vector<void* >::iterator iterv;
	list<void*>		pvlist;
	list<void*>::iterator	iterl;
	deque<void*>	pvdeque;
	deque<void* >::iterator	iterd;

	int i;

	int i1=1,i2=2,i3=3,i4=1,i5=2,i6=666;




/*
	for (i=0 ; i < 10 ; i++)  pvdeque.push_back(&i1);
	iterd=pvdeque.begin(); iterd++ ; iterd++ ;
	pvdeque.insert(iterd,&i2);

	cout<<" pvdeque content : " ;  for (iterd=pvdeque.begin() ;  iterd !=pvdeque.end() ; iterd++ ) cout<< *iterd << "   " ;  cout<<endl<< endl<< endl;
*/




/*
	pvlist.push_back(&i1);  pvlist.push_back(&i2);  pvlist.push_back(&i3);
	cout<<" pvlist content : " ;  for (iterl = pvlist.begin() ; iterl != pvlist.end(); iterl++ ) cout<< *iterl << "   " ;  cout<<endl;
	cout <<  "  size : " << pvlist.size() << endl;

	pvlist.reverse();
	cout<<" pvlist content : " ;  for (iterl = pvlist.begin() ; iterl != pvlist.end(); iterl++ ) cout<< *iterl << "   " ;  cout<<endl;
	cout <<  "  size : " << pvlist.size() << endl<< endl<< endl<< endl;;
*/



/*
	pvector.push_back(&i1);  pvector.push_back(&i2);  pvector.push_back(&i3); pvector.push_back(&i4);  pvector.push_back(&i5); pvector.push_back(&i6);
	cout<<" pvector content : " ;  	for (iterv = pvector.begin() ; iterv != pvector.end(); iterv++ ) cout<< *iterv << "   " ; 	cout<<endl;
	cout << "capacity : " << pvector.capacity() << "  size : " << pvector.size() << endl;

	iterv = pvector.begin() ; iterv++ ; iterv++ ; iterv++ ;
	//pvector.insert(3,&i6);
	pvector.insert(pvector.begin()+3,&i6);
	cout<<" pvector content : " ;  	for (iterv = pvector.begin() ; iterv != pvector.end(); iterv++ ) cout<< *iterv << "   " ; 	cout<<endl;
	cout << "capacity : " << pvector.capacity() << "  size : " << pvector.size() << endl;

	pvector.assign(5,&i1);
	cout<<" pvector content : " ;  	for (iterv = pvector.begin() ; iterv != pvector.end(); iterv++ ) cout<< *iterv << "   " ; 	cout<<endl;
	cout << "capacity : " << pvector.capacity() << "  size : " << pvector.size() << endl;


	pvector.reserve(3);
	cout<<" pvector content : " ;  	for (iterv = pvector.begin() ; iterv != pvector.end(); iterv++ ) cout<< *iterv << "   " ; 	cout<<endl;
	cout << "capacity : " << pvector.capacity() << "  size : " << pvector.size() << endl;

	pvector.resize(pvector.size()+1);
	cout<<" pvector content : " ;  	for (iterv = pvector.begin() ; iterv != pvector.end(); iterv++ ) cout<< *iterv << "   " ; 	cout<<endl;
	cout << "capacity : " << pvector.capacity() << "  size : " << pvector.size() << endl;

	cout << pvector.front() <<"       "<<pvector.back() << endl<< endl<< endl;
	pvector.begin() ;   pvector.end() ;

*/




	_vector<PVOID> _pvector(5);
	_vector<PVOID>::iterator _iterator,_iteratorend;

	_pvector.push_back(&i1); 
	_pvector.push_back(&i2); 
	_pvector.push_back(&i3); 
	_pvector.push_back(&i4); 
	_pvector.push_back(&i5); 
	_pvector.push_back(&i6);
	_iterator=_pvector.vbegin();
	_iteratorend=_pvector.vend();
	cout<<" _pvector content : " ;  
	for (  ; _iterator!= _iteratorend;_iterator++) 	
	{
		cout<< *_iterator << "   " ;
		
	}
	cout<<endl;
	cout << "capacity : " << _pvector.capacity() << "  size : " << _pvector.size() << endl<< endl;

	//cout << _pvector.front() <<"       "<<_pvector.back()<< endl<< endl;


	cout <<_pvector.erase(4)<<endl;
	cout<<" _pvector content : " ;  
	for ( _iterator=_pvector.vbegin() ; _iterator!=_pvector.vend() ;_iterator++) 	
	{
		cout<< *_iterator << "   " ;
		
	}
	cout<<endl;
	cout << "capacity : " << _pvector.capacity() << "  size : " << _pvector.size() <<endl<< endl;



	_pvector.push_front(&i1);	

	_pvector.push_back(&i1); _pvector.push_back(&i1);_pvector.push_back(&i1);_pvector.push_back(&i1);_pvector.push_back(&i1);

	_pvector.insert(8,&i2);

	//void* pv;
	cout<<" _pvector content : " ;	
	for ( _iterator=_pvector.vbegin() ; _iterator!=_pvector.vend() ;_iterator++) 
	{
		cout<< *_iterator << "   " ; 	
	}
	cout<<endl;
	cout << "capacity : " << _pvector.capacity() << "  size : " << _pvector.size() << endl<< endl;


	_pvector.resize(0);
	cout<<" _pvector content : " ;  
	for ( _iterator=_pvector.vbegin() ; _iterator!=_pvector.vend() ;)
	{
		cout<< *_iterator << "   " ;  	
		_iterator++;
	}
	cout<<endl;
	cout << "capacity : " << _pvector.capacity() << "  size : " << _pvector.size() << endl<< endl;


	_pvector.assign(10,&i2);
	cout<<" _pvector content : " ;  	
	for ( _iterator=_pvector.vbegin() ; _iterator!=_pvector.vend() ;_iterator+=3) 	
		cout<< *_iterator << "   " ; 	
	cout<<endl;
	cout << "capacity : " << _pvector.capacity() << "  size : " << _pvector.size() << endl<< endl<< endl;





	list<void*>		pvlist1;
	vector<void*>	pvector1;
	deque<void*>	pvdeque1;

	int j;

	DWORD dwStart,dwStop;




/*
	_vector<PVOID> _pvector1(1000);  //step= 1, 10 ,20 ,50 100 ,500,1000,10000

	dwStart = GetTickCount();
	for (i=0 ; i < 100000 ; i++)  pvector1.push_back(&i1); 
    dwStop = GetTickCount(); 

	cout<< "time1 :"<< dwStop-dwStart << "     "<< pvector1.size()<< endl;

	dwStart = GetTickCount();
	for (i=0 ; i < 100000 ; i++)  _pvector1.push_back(&i1); 
    dwStop = GetTickCount(); 

	cout<< "time2 :"<< dwStop-dwStart << "     "<< _pvector1.size()<< endl;
*/

/*
	_vector<PVOID> _pvector1(100);  //step= 1, 10 ,20 ,50 100 ,500,1000,10000


	dwStart = GetTickCount();
	for (i=0 ; i < 100000 ; i++)  pvlist1.push_front(&i1); 
    dwStop = GetTickCount(); 

	cout<< "time1 :"<< dwStop-dwStart << "     "<< pvlist1.size()<< endl;

	dwStart = GetTickCount();
	for (i=0 ; i < 100000 ; i++)  _pvector1.push_front(&i1); 
    dwStop = GetTickCount(); 

	cout<< "time2 :"<< dwStop-dwStart << "     "<< _pvector1.size()<< endl;
*/

/*
	_vector<PVOID> _pvector1(20);  //step= 1, 10 ,20 ,50 100 ,500,1000,10000


	dwStart = GetTickCount();
	for (i=0 ; i < 100000 ; i++)  pvlist1.push_back(&i1); 
    dwStop = GetTickCount(); 

	cout<< "time1 :"<< dwStop-dwStart << "     "<< pvlist1.size()<< endl;

	dwStart = GetTickCount();
	for (i=0 ; i < 100000 ; i++)  _pvector1.push_back(&i1); 
    dwStop = GetTickCount(); 

	cout<< "time2 :"<< dwStop-dwStart << "     "<< _pvector1.size()<< endl;
*/

/*
	_vector<PVOID> _pvector1(100);  //step= 1, 10 ,20 ,50 100 ,500,1000,10000


	dwStart = GetTickCount();
	for (i=0 ; i < 100000 ; i++)  pvdeque1.push_back(&i1); 
    dwStop = GetTickCount(); 

	cout<< "time1 :"<< dwStop-dwStart << "     "<< pvdeque1.size()<< endl;

	dwStart = GetTickCount();
	for (i=0 ; i < 100000 ; i++)  _pvector1.push_back(&i1); 
    dwStop = GetTickCount(); 

	cout<< "time2 :"<< dwStop-dwStart << "     "<< _pvector1.size()<< endl;
*/



/*
	_vector<PVOID> _pvector1(500);  //step= 1, 10 ,20 ,50 100 ,500,1000,10000


	for (i=0 ; i < 100000 ; i++)  pvlist1.push_back(&i1);
	for (i=0 ; i < 100000 ; i++)  _pvector1.push_back(&i1); 

	dwStart = GetTickCount();
	for (iterl=pvlist1.begin(),j=0 ; j<40000 && iterl !=pvlist1.end() ; j++ ,iterl++ ,iterl++)  pvlist1.insert(iterl,&i2); 
    dwStop = GetTickCount(); 

	cout<< "time1 :"<< dwStop-dwStart << "     "<< pvlist1.size()<< endl;

	dwStart = GetTickCount();
	for (i=0 ,j=0; j<40000 && i < 100000 ; j++,i++,i++)  _pvector1.insert(i,&i2); 
    dwStop = GetTickCount(); 

	cout<< "time2 :"<< dwStop-dwStart << "     "<< _pvector1.size()<< endl;
*/

/*
	_vector<PVOID> _pvector1(500);  //step= 1, 10 ,20 ,50 100 ,500,1000,10000  括号中为最佳值


	//for (i=0 ; i < 100000 ; i++)  pvector1.push_back(&i1);
	for (i=0 ; i < 100000 ; i++)  _pvector1.push_back(&i1); 

	dwStart = GetTickCount();
	for (iterv=pvector1.begin(),j=0 ; j<40000 && iterv !=pvector1.end() ; j++ ,iterv++ ，iterv++)  pvector1.insert(iterv,&i2);   //执行出错
    dwStop = GetTickCount(); 

	cout<< "time1 :"<< dwStop-dwStart << "     "<< pvector1.size()<< endl;


	dwStart = GetTickCount();
	for (i=0 ,j=0; j<40000 && i < 100000 ; j++,i++,i++)  _pvector1.insert(i,&i2); 
    dwStop = GetTickCount(); 

	cout<< "time2 :"<< dwStop-dwStart << "     "<< _pvector1.size()<< endl;
*/


/*
	_vector<PVOID> _pvector1(500);  //step= 1, 10 ,20 ,50 100 ,500,1000,10000


	//for (i=0 ; i < 100000 ; i++)  pvdeque1.push_back(&i1);
	for (i=0 ; i < 100000 ; i++)  _pvector1.push_back(&i1); 

	dwStart = GetTickCount();
	for (iterd=pvdeque1.begin(),j=0 ; j<40000 && iterd !=pvdeque1.end() ; j++ ,iterd++ ,iterd++)  pvdeque1.insert(iterd,&i2); 
    dwStop = GetTickCount(); 

	cout<< "time1 :"<< dwStop-dwStart << "     "<< pvdeque1.size()<< endl;

	dwStart = GetTickCount();
	for (i=0 ,j=0; j<40000 && i < 100000 ; j++,i++,i++)  _pvector1.insert(i,&i2); 
    dwStop = GetTickCount(); 

	cout<< "time2 :"<< dwStop-dwStart << "     "<< _pvector1.size()<<"      "<<  _pvector1.capacity()<< endl; 
	//cout<<" _pvector1 content : " ;  for ( i=0 ; i<100 ; i++) cout<< _pvector1[i] << "   " ;  cout<<endl;
*/


	_vector<PVOID>* _ppvector;

/*
	for(j=50; j<=1000;j+=50)  //100000, step=  550,250,400,850,1000 ;50000, step=  450,650 ;20000, step=  250 ;10000, step=  50 ;
	{
		_ppvector=new _vectorpv(j);  
		dwStart = GetTickCount();
		for (i=0 ; i < 100000 ; i++)  _ppvector->push_back(&i1); 
		dwStop = GetTickCount(); 

		cout<< "time-"<<j<<" :"<< dwStop-dwStart << "     "<< _ppvector->size()<< endl;
		cout<<" _ppvector content : " ;  for ( i=0 ;i<15&& i<_ppvector->size() ; i++) cout<< _ppvector->value(i) << "   " ;  cout<<endl<<endl<<endl;
		delete _ppvector;
	}
*/

/*
	int k;

	for(j=50; j<=1000;j+=50)  //40000, step=  400,450 ;30000, step=  500,400,350 ; 20000, step=  400,500,600,350 ;10000, step=  450,350,500,550 ;5000, step= 600 ;
	{
		_ppvector=new _vectorpv(j); 
		for (i=0 ; i < 100000 ; i++)  _ppvector->push_back(&i1); 
		dwStart = GetTickCount();
		for (i=0 ,k=0; k<40000 && i < 100000 ; k++,i+=2)  _ppvector->insert(i,&i1); 
		dwStop = GetTickCount(); 

		cout<< "time-"<<j<<" :"<< dwStop-dwStart << "     "<< _ppvector->size()<< endl;
		cout<<" _ppvector content : " ;  for ( i=0 ;i<15&& i<_ppvector->size() ; i++) cout<< _ppvector->value(i) << "   " ;  cout<<endl<<endl<<endl;
		delete _ppvector;
	}
*/

/*
	for(j=20; j<=200;j+=5)  //100000, step=  40,50,60, ;50000, step=   ;20000, step=   ;10000, step=   ;5000, step=   ;
	{
		_ppvector=new _vectorpv(j);  
		dwStart = GetTickCount();
		for (i=0 ; i < 100000 ; i++)  _ppvector->push_front(&i1); 
		dwStop = GetTickCount(); 

		cout<< "time-"<<j<<" :"<< dwStop-dwStart << "     "<< _ppvector->size()<< endl;
		cout<<" _ppvector content : " ;  for ( i=0 ;i<15&& i<_ppvector->size() ; i++) cout<< _ppvector->value(i) << "   " ;  cout<<endl<<endl<<endl;
		delete _ppvector;
	}
*/

	typedef pair<int, int> PAIRDINTINT;

	_pvector.push_back(&PAIRDINTINT(100,200));
	PAIRDINTINT* ppa= (PAIRDINTINT*)_pvector.pop_back();
	cout<<ppa->first<<"    "<<ppa->second<<endl<<endl;



	_vector<struct xyzrhw_uv_t> _vector_xyzrhw_uv_t;
	struct xyzrhw_uv_t _t_xyzrhw_uv_t, new_t_xyzrhw_uv_t;

	_t_xyzrhw_uv_t.uv = 1.1;
	_t_xyzrhw_uv_t.xyzrhw = 2.2;
	_vector_xyzrhw_uv_t.push_back(_t_xyzrhw_uv_t);
	new_t_xyzrhw_uv_t = _vector_xyzrhw_uv_t.pop_back();

	cout<<new_t_xyzrhw_uv_t.uv<< "        "<< new_t_xyzrhw_uv_t.xyzrhw<<endl<<endl;




	_vector<A> _vectorA(5);
	A a, new_a;

	//_vectorA.push_back(a);
	//_vectorA.assign(8,a);
	_vectorA.resize(8);
	new_a = _vectorA.pop_back();
	new_a.print();


	//_vector<byte4>										effect_part_model_drawable_index_vec[5];


	/*
	pvqueue.push(&i1);  pvqueue.push(&i2);  pvqueue.push(&i3);
	cout <<  "  size : " << pvqueue.size() << endl;
	pvqueue.pop();
	cout <<  "  size : " << pvqueue.size() << endl;

	cout << pvqueue.front() << "        " << pvqueue.back() << endl<< endl<< endl;
*/

	queue<int*>	queue;


	_queue<int*> _queue;

	_queue.push(&i1);  _queue.push(&i2);  _queue.push(&i3);
	cout <<  "  size : " << _queue.size() << endl;
	cout << _queue.front() << "        " << _queue.back() << endl;

	_queue.pop();
	cout <<  "  size : " << _queue.size() << endl;
	cout << _queue.front() << "        " << _queue.back() << endl<< endl<< endl;


	return 0;
}


