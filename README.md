# KShiftsClustering

[![Build Status](https://travis-ci.org/rened/KShiftsClustering.jl.png)](https://travis-ci.org/rened/KShiftsClustering.jl)
[![Build Status](http://pkg.julialang.org/badges/KShiftsClustering_0.4.svg)](http://pkg.julialang.org/?pkg=KShiftsClustering&ver=0.4)
[![Build Status](http://pkg.julialang.org/badges/KShiftsClustering_0.5.svg)](http://pkg.julialang.org/?pkg=KShiftsClustering&ver=0.5)


Provides an implementation of KShifts [1], fast method for data clustering, yielding results similar to kmeans in a fraction of the time. It updates its cluster centers using one data point at a time, so it is well suited for streaming clustering. A helper function for obtaining medoids is included.

[1] [Efficient and Distinct Large Scale Bags of Words](http://citeseerx.ist.psu.edu/viewdoc/download?doi=10.1.1.187.1423&rep=rep1&type=pdf)

Pseudo code:

```
Randomly sample k centers from data
for sample in data
    shift the clostest center into the direction of sample
```

#### Usage

```jl
data = rand(2,1000)
centers = kshifts(data,10)
labels = kshiftslabels(data, centers)
```

To perform online clustering, you can update the cluster centers with more data:
```jl
data = rand(2,1000)
centers = kshifts(data,10)
for i = 1:100
    data = rand(2,10)
    kshifts!(centers, data)
end
```

To get the original data points which are closest to the found centers use `kshiftsmedoids`:

```jl
centers, IDs = kshiftsmedoids(data, k)
```

#### Performance

KShifts is very fast, and has very low memory requirements:

```jl
data = rand(2,100_000)

@time begin
    centers = kshifts(data,10)
    labels = kshiftslabels(data, centers)
end
#  =>  elapsed time: 0.026258854 seconds (401400 bytes allocated)

# using kmeans from Clustering.jl:
@time kmeans(data,10)
#  =>  elapsed time: 2.329909439 seconds (1723782768 bytes allocated, 53.93% gc time) 
```

#### Results

Finally, let's look at some results:

```jl
using PyPlot, KShiftsClustering, FunctionalData
data = @p map unstack(1:10) (x->10*randn(2,1).+randn(2,1000)) | flatten

centers = kshifts(data,10)
labels = kshiftslabels(data,centers)

scatter(data[1,:], data[2,:], c=labels, marker = "o", edgecolor = "none")
plot(centers[1,:], centers[2,:], "ro")
```
![](example.png)
