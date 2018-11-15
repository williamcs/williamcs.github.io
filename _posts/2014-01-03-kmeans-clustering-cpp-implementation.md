---
title: "k Means clustering c++ implementation"
date: 2014-01-03
tags: [c++, machine learning]
excerpt: "c++, machine learning"
---

KMeans is a very common unsupervised learning algorithm, below is the algorithm.
<img src="{{ site.url }}{{ site.baseurl }}/images/old/kmeans.jpeg" alt="linearly separable data">

c++ implementation:
```cpp
#include <iostream>
#include <fstream>
#include <string>
#include <stdlib.h>
#include <time.h>

using namespace std;

// get the all the lines of the file
int getFileLines(string fileName)
{
	string line;
	ifstream inf;
	inf.open(fileName);

	if(!inf)
	{
		cerr << "Error: file could not be opened" << endl;
		exit(1);
	}

	int total = 0;
	while (!inf.eof())
	{
		getline(inf, line);
		++total;
	}

	inf.close();

	return total;
}

/**
	get file columns through a delimiter, because all lines in the test files
	has the same columns, so just count the first line to get the file column
**/
int getFileColumns(string fileName, string delimiter)
{
	string line;
	ifstream inf;
	inf.open(fileName);

	if(!inf)
	{
		cerr << "Error: file could not be opened" << endl;
		exit(1);
	}

	int column = 0;
	getline(inf, line);

	int start, end;
	start = 0;
	end = line.find(delimiter);

	while(end != std::string::npos)
	{
		//line.substr(start, end - start);
		column++;
		start = end + 1;
		end = line.find(delimiter, start);
	}

	column++;
	inf.close();

	//cout << "column is " << column << endl;

	return column;
}

/*
	get max and min value of an array
*/
template<class T>
void getMaxMin(T array[], int size, T& min, T& max)
{
	min = max = array[0];

	for (int i = 0; i < size; i++)
	{
		if (array[i] > max)
		{
			max = array[i];
		}
		else if (array[i] < min)
		{
			min = array[i];
		}
	}
}

/*
	create two dimensional double array, allocate the memory
*/
double** new2DArray(int m, int n)
{
	double** resultSet;

	resultSet = new double*[m];
	for (int i = 0; i < m; i++)
	{
		resultSet[i] = new double[n];
	}

	return resultSet;
}

/*
	create two dimensional array, allocate the memory
*/
template<class T>
void new2DArray(T** resultSet, int m, int n)
{
	resultSet = new T*[m];
	for (int i = 0; i < m; i++)
	{
		resultSet[i] = new T[n];
	}
}

/*
	delete two dimensional array, free the allocated memory
*/
template<class T>
void delete2DAarray(T** twoDArray, int m)
{
	for (int i = 0; i < m; i++)
	{
		delete[] twoDArray[i];
	}
	delete[] twoDArray;
}

/*
	get the data set of a file and return to a two dimensional array.
*/
double** getDataSet(string fileName, string delimiter)
{
	double** resultSet;
	int fileLines = getFileLines(fileName);
	int fileColumns = getFileColumns(fileName, delimiter);

	resultSet = new2DArray(fileLines, fileColumns);

	ifstream inf;
	inf.open(fileName);

	if(!inf)
	{
		cerr << "Error: file could not be opened" << endl;
		exit(1);
	}

	for (int i = 0; i < fileLines; i++)
	{
		for (int j = 0; j < fileColumns; j++)
		{
			inf >> resultSet[i][j];
			cout << resultSet[i][j] << " ";
		}
		cout << endl;
	}

	return resultSet;
}

/*
	get the data set of a file and return to a two dimensional array.
*/
double** getDataSet(string fileName, int fileLines, int fileColumns)
{
	double** resultSet;
	resultSet = new2DArray(fileLines, fileColumns);

	ifstream inf;
	inf.open(fileName);

	if(!inf)
	{
		cerr << "Error: file could not be opened" << endl;
		exit(1);
	}

	for (int i = 0; i < fileLines; i++)
	{
		for (int j = 0; j < fileColumns; j++)
		{
			inf >> resultSet[i][j];
			cout << resultSet[i][j] << " ";
		}
		cout << endl;
	}

	return resultSet;
}

/*
	create k random cluster centers, each center between the dimensional bounds.
*/
double** randCent(double** dataSet, int m, int n, int k)
{
	srand (time(NULL));

	double** centroids;
	centroids = new2DArray(k, n);

	for (int j = 0; j < n; j++)
	{
		// get max and min value of column j
		double max = dataSet[0][j];
		double min = dataSet[0][j];

		for(int i = 0; i < m; i++)
		{
			if (dataSet[i][j] > max)
			{
				max = dataSet[i][j];
			}
			else if (dataSet[i][j] < min)
			{
				min = dataSet[i][j];
			}
		}

		// get the range of column j
		double range = max - min;

		// get k random centroids
		for(int i = 0; i < k; i++)
		{
			centroids[i][j] = min + (double)rand() / ( RAND_MAX + 1) * range;
			//centroids[i][j] = min + range * rand() / ((double) RAND_MAX);
			cout << centroids[i][j] << " ";
		}
		cout << endl;
	}

	return centroids;
}

// calculate the two vectors' euclid distance
double disteuclid(double *vecA, double *vecB, int size)
{
	double dresult = 0.0;

	for (int i = 0; i < size; i++)
	{
		dresult += pow(vecA[i] - vecB[i], 2);
	}

	return sqrt(dresult);
}

/*
	get data set mean values, assign the values to a n column array
*/
double* getdataSetMean(double** dataSet, int m, int n)
{
	double* result = new double[n];

	for (int j = 0; j < n; j++)
	{
		double sum = 0.0;
		for (int i = 0; i < m; i++)
		{
			sum += dataSet[i][j];
		}
		result[j] = sum / m;
	}

	return result;
}

/*
	get data set mean values of a specified cluster, assign the values to a n column array
	parameter cent is the cluster index
*/
double* getdataSetMean(double** dataSet, double** clustersRecord, int m, int n, int cent)
{
	double* result = new double[n];

	for (int j = 0; j < n; j++)
	{
		double sum = 0.0;
		int count = 0;
		for (int i = 0; i < m; i++)
		{
			if ((int)clustersRecord[i][0] == cent)
			{
				++count;
				sum += dataSet[i][j];
			}

		}
		result[j] = sum / count;
	}

	return result;
}

/*
	recalculate the k cluster centroids, assign the centroid to mean of that cluster values
*/
void getNewCentroids(double** dataSet, double** centroids, double** clustersRecord, int m, int n, int k)
{
	int* kcount = new int[k];
	memset(kcount, 0, k * sizeof(int));
	/*for (int i = 0; i < k; i++)
	{
		kcount[i] = 0;
	}*/

	// it seems that memset can't initialize this 2d array
	//memset(centroids, 0.0, k * n * sizeof(centroids[0][0]));
	for (int i = 0; i < k; i++)
	{
		for (int j = 0; j < n; j++)
		{
			centroids[i][j] = 0.0;
		}
	}

	for (int i = 0; i < m; i++)
	{
		int kk = (int)clustersRecord[i][0];
		kcount[kk] += 1;

		for (int j = 0; j < n; j++)
		{
			centroids[kk][j] += dataSet[i][j];
		}
	}

	for (int i = 0; i < k; i++)
	{
		for (int j = 0; j < n; j++)
		{
			centroids[i][j] = centroids[i][j] / kcount[i];
		}
	}
}

/*
	implement kmeans algorithm
*/
void kMeans(double** dataSet, int m, int n, int k)
{
	double** clustersRecord;	// clustersRecord is a two dimensional array with 2 columns,
								// first the cluster index, second the euclid distance
	clustersRecord = new2DArray(m, 2);

	// get k random centroids
	double** centroids = randCent(dataSet, m, n, k);
	bool clusterChanged = true;

	int count = 0;	// count is the number when to converge

	while (clusterChanged)
	{
		clusterChanged = false;

		for (int i = 0; i < m; i++)
		{
			double minDist = 100000;
			int minIndex = -1;

			for (int j = 0; j < k; j++)
			{
				double distJ = disteuclid(centroids[j], dataSet[i], n);
				if (distJ < minDist)
				{
					minDist = distJ;
					minIndex = j;
				}
			}

			if (clustersRecord[i][0] != minIndex)
			{
				clusterChanged = true;
			}

			clustersRecord[i][0] = minIndex;
			clustersRecord[i][1] = minDist;
		}

		count++;
		cout << "The change time is : " << count << endl;

		// recalculate controids method 1
		getNewCentroids(dataSet, centroids, clustersRecord, m, n, k);

		//// recalculate controids method 2
		//for (int cent = 0; cent < k; cent++)
		//{
		//	// get all the points in this cluster and assign centroid to mean
		//	centroids[cent] = getdataSetMean(dataSet, clustersRecord, m, n, cent);
		//}
	}

	// print the cluster records, the first colum is the point belong to which cluster,
	// the second column is distance to that centroid
	for (int i = 0; i < m; i++)
	{
		cout << "clustersRecord[" << i << "][0] : " << clustersRecord[i][0] << " clustersRecord[" << i << "][1] : " << clustersRecord[i][1] << endl;
	}

	delete2DAarray(centroids, k);
	delete2DAarray(clustersRecord, m);
}


int main()
{
	string fileName = "test.txt";

	// test get file data set
	int fileLines = getFileLines(fileName);
	int fileColumns = getFileColumns(fileName, "\t");
	double** dataSet = getDataSet(fileName, fileLines, fileColumns);

	//// test randCent
	//cout << "get randCent: " << endl;
	//randCent(dataSet, fileLines, fileColumns, 4);

	// test kmeans
	cout << "kmeans test: " << endl;
	kMeans(dataSet, fileLines, fileColumns, 4);

	delete2DAarray(dataSet, fileLines);

	cout << "the end" << endl;

	return 0;
}

```

Below is the test.txt data set.
```
1.658985 4.285136  
-3.453687   3.424321  
4.838138    -1.151539  
-5.379713   -3.362104  
0.972564    2.924086  
-3.567919   1.531611  
0.450614    -3.302219  
-3.487105   -1.724432  
2.668759    1.594842  
-3.156485   3.191137  
3.165506    -3.999838  
-2.786837   -3.099354  
4.208187    2.984927  
-2.123337   2.943366  
0.704199    -0.479481  
-0.392370   -3.963704  
2.831667    1.574018  
-0.790153   3.343144  
2.943496    -3.357075  
-3.195883   -2.283926  
2.336445    2.875106  
-1.786345   2.554248  
2.190101    -1.906020  
-3.403367   -2.778288  
1.778124    3.880832  
-1.688346   2.230267  
2.592976    -2.054368  
-4.007257   -3.207066  
2.257734    3.387564  
-2.679011   0.785119  
0.939512    -4.023563  
-3.674424   -2.261084  
2.046259    2.735279  
-3.189470   1.780269  
4.372646    -0.822248  
-2.579316   -3.497576  
1.889034    5.190400  
-0.798747   2.185588  
2.836520    -2.658556  
-3.837877   -3.253815  
2.096701    3.886007  
-2.709034   2.923887  
3.367037    -3.184789  
-2.121479   -4.232586  
2.329546    3.179764  
-3.284816   3.273099  
3.091414    -3.815232  
-3.762093   -2.432191  
3.542056    2.778832  
-1.736822   4.241041  
2.127073    -2.983680  
-4.323818   -3.938116  
3.792121    5.135768  
-4.786473   3.358547  
2.624081    -3.260715  
-4.009299   -2.978115  
2.493525    1.963710  
-2.513661   2.642162  
1.864375    -3.176309  
-3.171184   -3.572452  
2.894220    2.489128  
-2.562539   2.884438  
3.491078    -3.947487  
-2.565729   -2.012114  
3.332948    3.983102  
-1.616805   3.573188  
2.280615    -2.559444  
-2.651229   -3.103198  
2.321395    3.154987  
-1.685703   2.939697  
3.031012    -3.620252  
-4.599622   -2.185829  
4.196223    1.126677  
-2.133863   3.093686  
4.668892    -2.562705  
-2.793241   -2.149706  
2.884105    3.043438  
-2.967647   2.848696  
4.479332    -1.764772  
-4.905566   -2.911070

```
