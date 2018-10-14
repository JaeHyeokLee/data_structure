# 3. Array

3-1. Polynomial

1) Store All terms in Array

~~~c
// Add polynomial with array
#include <stdio.h>
#define MAX(a, b) (((a)>(b))?(a):(b))
#define MAX_DEGREE 101

// Struct of Polynomial
typedef struct{
    int degree;
    float coef[MAX_DEGREE];
} polynomial;

polynomial poly_add(polynomial A, polynomial B)
{
    polynomial C;
    int Apos = 0, Bpos = 0, Cpos = 0;
    int degree_a = A.degree;
    int degree_b = B.degree;
    
    C.degree = MAX(A.degree, B.degree);
    
    while(Apos<=A.degree && Bpos<=B.degree)
    {
        if(degree_a > degree_b)
        {
            C.coef[Cpos++] = A.coef[Apos++];
            degree_a--;
        }
        
        else if(degree_a == degree_b)
        {
            C.coef[Cpos++] = A.coef[Apos++] + B.coef[Bpos++];
            degree_a--; degree_b--;
        }
        
        else
        {
            C.coef[Cpos++] = B.coef[Bpos++];
            begree_b--;
        }
    }
    return C;
}

main()
{
    polynomial a = {5, {3, 6, 0, 0, 0, 10}};
    polynomial b = {4, {7, 0, 5, 0, 1}};
    polynomial c;
    c = poly_add(a, b);
}
~~~

2) Store Non-Zero Terms in array

~~~c
#define MAX_TERMS 101

struct {
    float coef;
    int expon;
} terms[MAX_TERMS] = { {8, 3}, {7, 1}, {1, 0}, {10, 3}, {3, 2}, {1, 0}};

int avail = 6;

char compare(int a, int b)
{
    if(a > b) return '>';
    else if(a == b) return '=';
    else return '<';
}

void attach(float coef, int expon)
{
    if( avail > MAX_TERMS )
    {
        fprintf(stderr, "Too Many Terms");
        exit(1);
    }
    terms[avail].coef = coef;
    terms[avail++].expon = expon;
}

poly_add(int As, int Ae, int Bs, int Be, int *Cs, int *Ce)
{
    float tempcoef;
    *Cs = avail;
    
    while( As <= Ae && Bs <= Be ){
        switch(compare(terms[As].expon, terms[Bs].expon)){
            case '>':
                attach(terms[As].coef, terms[As].expon);
                As++;
                break;
            case '=':
                tempcoef = terms[As].coef + terms[Bs].coef;
                if( tempcoef )
                    attach(tempcoef, terms[As].expon);
                As++; Bs++;
                break;
            case '<':
                attach(terms[Bs].coef, terms[Bs].expon);
                Bs++;
                break;
        }
    }
    for(; As <= Ae ; As ++)
        attach(terms[As].coef, terms[As].expon);
    for(; Bs <= Be ; Bs ++)
        attach(terms[Bs].coef, terms[Bs].expon);
    *Ce = avail = 1;
}

void main()
{
    int Cs, Ce;
    poly_add(0, 2, 3, 5, &Cs, &Ce);
}


3-2. Sparse Matrix

1) Store All members in array

~~~c

#include<stdio.h>
#define ROWS 3
#define COLS 3

void sparse_matrix_add(int A[ROWS][COLS], int B[ROWS][COLS], int C[ROWS][COLS])
{
    int r, c;
    for(r = 0 ; r < ROWS ; r ++){
        for(c = 0; c < COLS ; c++){
            C[r][c] = A[r][c] + B[r][c];
        }
    }
}

main()
{
    int array1[ROWS][COLS] = { {2, 3, 0}, {8, 9, 1}, {7, 0, 5} };
    int array2[ROWS][COLS] = { {1, 0, 0}, {1, 0, 0}, {1, 0, 0} };
    
    int array3[ROWS][COLS];
    sparse_matrix_add(array1, array2, array3);
}
~~~

2) Store Non-zero members with row index and col index

~~~c
#include <stdio.h>
#include <stdlib.h>
#define ROWS 3
#define COLS 3
#define MAX_TERMS 10

typedef struct {
    int row;
    int col;
    int value;
} element;

typedef struct SparseMatrix {
    element data[MAX_TERMS];
    int rows;
    int cols;
    int terms;
} SparseMatrix;

SparseMatrix sparse_matrix_add(SparseMatrix a, SparseMatrix b)
{
    SparseMatrix c;
    int ca = 0, cb = 0, cc = 0;
    
    if (a.rows != b.rows || a.cols != b.cols){
        fprintf(stderr, "Sparse Matrix Dimension Error");
        exit(1);
    }
    
    c.rows = a.rows;
    c.cols = a.cols;
    c.terms = 0;
    
    while( ca < a.terms && cb < b.terms )
    {
        int inda = a.data[ca].row * a.cols + a.data[ca].col;
        int indb = b.data[cb].row * b.cols + b.data[cb].col;
        
        if(inda < indb){
            c.data[cc++] = a.data[ca++];
        }
        else if(inda == indb){
            if((a.data[ca].value + b.data[cb].value) != 0){
                c.data[cc].row = a.data[ca].row;
                c.data[cc].col = a.data[ca].col;
                c.data[cc++].value = a.data[ca++].vaule + b.data[cb++].value;
            }
            else{
                ca++; cb++;
            }
        }
        else
            c.data[cc++] = b.data[cb++];
    }
    for(; ca < a.terms ; )
        c.data[cc++] = a.data[ca++];
    for(; cb < b.terms ; )
        c.data[cc++] = b.data[cb++];
    c.terms = cc;
    return c;
}

main()
{
    SparseMatrix m1 = { {{1, 1, 5}, {2, 2, 9}}, 3, 3, 2};
    SparseMatrix m2 = { {{0, 0, 5}, {2, 2, 9}}, 3, 3, 2};
    
    SparseMatrix m3;
    m3 = sparse_matrix_add(m1, m2);
}
~~~
            
