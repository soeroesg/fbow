FBOW

This is a modified version of the [original FBoW](https://github.com/rmsalinas/fbow) by [@rmsalinas](https://github.com/rmsalinas).
Some changes were taken over from the [already modified FBoW](https://github.com/shinsumicco/FBoW) by [@shinsumicco](https://github.com/shinsumicco).

=====
FBoW (Fast Bag of Words) is an extremely optimized version of the [DBoW2](https://github.com/dorian3d/DBoW2)/[DBoW3](https://github.com/rmsalinas/DBow3) libraries.  
The library is highly optimized to speed up the Bag of Words creation using AVX,SSE and MMX instructions.  
In loading a vocabulary, FBoW is about 80x faster than DBoW2.  
In transforming an image into Bag of Words using on machines with AVX instructions, it is about 6.4x faster.  

## 
## Main features:
	* Only depends on OpenCV 
	* Any type of descriptors allowed out of the box (binary and real)
	* Dictionary creation from a set of images. Bugs found in DBOW2/3 corrected.
	* Extremmely fast bow creation using specialized versions using AVX,SSE and MMX instructions both for binary and floating point descriptors.
	* Very fast load of vocabularies

## 
## The main differences with DBOW2/3 are:

	* Not yet implemented indexing of images. 


## Citing
If you use this project in academic research you must cite us. This project is part of the ucoslam project. Visit [ucoslam.com](http://ucoslam.com) for more information


## Vocabularies
In directory vocabularies you have one already prepared for orb.

## Test speed
Go to test and run the program test_dbow2VSfbow

## License
This software is distributed under MIT License.
See the [LICENSE](./LICENSE).
