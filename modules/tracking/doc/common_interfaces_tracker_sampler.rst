Common Interfaces of TrackerSampler
===================================

.. highlight:: cpp


TrackerSampler
--------------

Class that manages the sampler in order to select regions for the update the model of the tracker

[AAM]_ Sampling e Labeling. See table I and section III B

.. ocv:class:: TrackerSampler

TrackerSampler class::

   class CV_EXPORTS_W TrackerSampler
   {
    public:

     TrackerSampler();
     ~TrackerSampler();

     void sampling( const Mat& image, Rect boundingBox );

     const std::vector<std::pair<String, Ptr<TrackerSamplerAlgorithm> > >& getSamplers() const;
     const std::vector<Mat>& getSamples() const;

     bool addTrackerSamplerAlgorithm( String trackerSamplerAlgorithmType );
     bool addTrackerSamplerAlgorithm( Ptr<TrackerSamplerAlgorithm>& sampler );

   };


TrackerSampler is an aggregation of :ocv:class:`TrackerSamplerAlgorithm`

.. seealso::

   :ocv:class:`TrackerSamplerAlgorithm`

TrackerSampler::sampling
------------------------

Computes the regions starting from a position in an image

.. ocv:function::  void TrackerSampler::sampling( const Mat& image, Rect boundingBox )

   :param image: The current frame

   :param boundingBox: The bounding box from which regions can be calculated


TrackerSampler::getSamplers
---------------------------

Return the collection of the :ocv:class:`TrackerSamplerAlgorithm`

.. ocv:function:: const std::vector<std::pair<String, Ptr<TrackerSamplerAlgorithm> > >& TrackerSampler::getSamplers() const


TrackerSampler::getSamples
--------------------------

Return the samples from all :ocv:class:`TrackerSamplerAlgorithm`, [AAM]_ Fig. 1 variable Sk

.. ocv:function:: const std::vector<Mat>& TrackerSampler::getSamples() const

TrackerSampler::addTrackerSamplerAlgorithm
------------------------------------------

Add TrackerSamplerAlgorithm in the collection.
Return true if sampler is added, false otherwise

.. ocv:function:: bool TrackerSampler::addTrackerSamplerAlgorithm( String trackerSamplerAlgorithmType )

   :param trackerSamplerAlgorithmType: The TrackerSamplerAlgorithm name

.. ocv:function:: bool TrackerSampler::addTrackerSamplerAlgorithm( Ptr<TrackerSamplerAlgorithm>& sampler )

   :param sampler: The TrackerSamplerAlgorithm class


The modes available now:

* ``"CSC"`` -- Current State Center

* ``"CS"`` -- Current State

* ``"PF"`` -- Particle Filtering

Example ``TrackerSamplerAlgorithm::addTrackerSamplerAlgorithm`` : ::

    //sample usage:

     TrackerSamplerCSC::Params CSCparameters;
     Ptr<TrackerSamplerAlgorithm> CSCSampler = new TrackerSamplerCSC( CSCparameters );

     if( !sampler->addTrackerSamplerAlgorithm( CSCSampler ) )
       return false;

     //or add CSC sampler with default parameters
     //sampler->addTrackerSamplerAlgorithm( "CSC" );


.. note:: If you use the second method, you must initialize the TrackerSamplerAlgorithm


TrackerSamplerAlgorithm
-----------------------

Abstract base class for TrackerSamplerAlgorithm that represents the algorithm for the specific sampler.

.. ocv:class:: TrackerSamplerAlgorithm

TrackerSamplerAlgorithm class::

   class CV_EXPORTS_W TrackerSamplerAlgorithm
   {
    public:

     virtual ~TrackerSamplerAlgorithm();

     static Ptr<TrackerSamplerAlgorithm> create( const String& trackerSamplerType );

     bool sampling( const Mat& image, Rect boundingBox, std::vector<Mat>& sample );
     String getClassName() const;
   };

TrackerSamplerAlgorithm::create
-------------------------------

Create TrackerSamplerAlgorithm by tracker sampler type.

.. ocv:function:: static Ptr<TrackerSamplerAlgorithm> TrackerSamplerAlgorithm::create( const String& trackerSamplerType )

   :param trackerSamplerType: The trackerSamplerType name

The modes available now:

* ``"CSC"`` -- Current State Center

* ``"CS"`` -- Current State


TrackerSamplerAlgorithm::sampling
---------------------------------

Computes the regions starting from a position in an image. Return true if samples are computed, false otherwise

.. ocv:function:: bool TrackerSamplerAlgorithm::sampling( const Mat& image, Rect boundingBox, std::vector<Mat>& sample )

   :param image: The current frame

   :param boundingBox: The bounding box from which regions can be calculated

   :sample: The computed samples [AAM]_ Fig. 1 variable Sk

TrackerSamplerAlgorithm::getClassName
-------------------------------------

Get the name of the specific TrackerSamplerAlgorithm

.. ocv:function::  String TrackerSamplerAlgorithm::getClassName() const

Specialized TrackerSamplerAlgorithm
===================================

In [AAM]_ table I there are described the most known sampling strategies. At moment :ocv:class:`TrackerSamplerCSC` and :ocv:class:`TrackerSamplerCS` are implemented. Beside these, there is :ocv:class:`TrackerSamplerPF`, sampler based on particle filtering.

TrackerSamplerCSC : TrackerSamplerAlgorithm
-------------------------------------------

TrackerSampler based on CSC (current state centered), used by MIL algorithm TrackerMIL

.. ocv:class:: TrackerSamplerCSC

TrackerSamplerCSC class::


   class CV_EXPORTS_W TrackerSamplerCSC
   {
    public:

     TrackerSamplerCSC( const TrackerSamplerCSC::Params &parameters = TrackerSamplerCSC::Params() );
     void setMode( int samplingMode );

     ~TrackerSamplerCSC();
   };


TrackerSamplerCSC::Params
-------------------------

.. ocv:struct:: TrackerSamplerCSC::Params

List of TrackerSamplerCSC parameters::

   struct CV_EXPORTS Params
   {
    Params();
    float initInRad;        // radius for gathering positive instances during init
    float trackInPosRad;    // radius for gathering positive instances during tracking
    float searchWinSize;    // size of search window
    int initMaxNegNum;      // # negative samples to use during init
    int trackMaxPosNum;     // # positive samples to use during training
    int trackMaxNegNum;     // # negative samples to use during training
   };


TrackerSamplerCSC::TrackerSamplerCSC
------------------------------------

Constructor

.. ocv:function:: TrackerSamplerCSC::TrackerSamplerCSC( const TrackerSamplerCSC::Params &parameters = TrackerSamplerCSC::Params() )

    :param parameters: TrackerSamplerCSC parameters :ocv:struct:`TrackerSamplerCSC::Params`

TrackerSamplerCSC::setMode
--------------------------

Set the sampling mode of TrackerSamplerCSC

.. ocv:function:: void TrackerSamplerCSC::setMode( int samplingMode )

    :param samplingMode: The sampling mode

The modes are:

* ``"MODE_INIT_POS = 1"`` -- for the positive sampling in initialization step
* ``"MODE_INIT_NEG = 2"`` -- for the negative sampling in initialization step
* ``"MODE_TRACK_POS = 3"`` -- for the positive sampling in update step
* ``"MODE_TRACK_NEG = 4"`` -- for the negative sampling in update step
* ``"MODE_DETECT = 5"`` -- for the sampling in detection step

TrackerSamplerCS : TrackerSamplerAlgorithm
-------------------------------------------

TrackerSampler based on CS (current state), used by algorithm TrackerBoosting

.. ocv:class:: TrackerSamplerCS

TrackerSamplerCS class::


   class CV_EXPORTS_W TrackerSamplerCS
   {
    public:

     TrackerSamplerCS( const TrackerSamplerCS::Params &parameters = TrackerSamplerCS::Params() );
     void setMode( int samplingMode );

     ~TrackerSamplerCS();
   };


TrackerSamplerCS::Params
-------------------------

.. ocv:struct:: TrackerSamplerCS::Params

List of TrackerSamplerCS parameters::

   struct CV_EXPORTS Params
   {
    Params();
    float overlap;  //overlapping for the search windows
    float searchFactor; //search region parameter
   };


TrackerSamplerCS::TrackerSamplerCS
------------------------------------

Constructor

.. ocv:function:: TrackerSamplerCS::TrackerSamplerCS( const TrackerSamplerCS::Params &parameters = TrackerSamplerCS::Params() )

    :param parameters: TrackerSamplerCS parameters :ocv:struct:`TrackerSamplerCS::Params`

TrackerSamplerCS::setMode
--------------------------

Set the sampling mode of TrackerSamplerCS

.. ocv:function:: void TrackerSamplerCS::setMode( int samplingMode )

    :param samplingMode: The sampling mode

The modes are:

* ``"MODE_POSITIVE = 1"`` -- for the positive sampling
* ``"MODE_NEGATIVE = 2"`` -- for the negative sampling
* ``"MODE_CLASSIFY = 3"`` -- for the sampling in classification step

TrackerSamplerPF : TrackerSamplerAlgorithm
-------------------------------------------

This sampler is based on particle filtering. In principle, it can be thought of as performing some sort of optimization (and indeed, this
tracker uses opencv's ``optim`` module), where tracker seeks to find the rectangle in given frame, which is the most *"similar"* to the initial
rectangle (the one, given through the constructor). 

The optimization performed is stochastic and somehow resembles genetic algorithms, where on each new ``image`` received (submitted via ``TrackerSamplerPF::sampling()``) we start with the region bounded by ``boundingBox``, then generate several "perturbed" boxes, take the ones most similar to the original. This selection round is repeated several times. At the end, we hope that only the most promising box remaining, and these are combined to produce the subrectangle of ``image``, which is put as a sole element in array ``sample``.

It should be noted, that the definition of "similarity" between two rectangles is based on comparing their histograms. As experiments show, tracker is *not* very succesfull if target is assumed to strongly change its dimensions.

.. ocv:class:: TrackerSamplerPF

TrackerSamplerPF class::

   class CV_EXPORTS_W TrackerSamplerPF : public TrackerSamplerAlgorithm{
   public:
     TrackerSamplerPF(const Mat& chosenRect,const TrackerSamplerPF::Params &parameters = TrackerSamplerPF::Params());
     void sampling( const Mat& image, Rect boundingBox, std::vector<Mat>& sample ); //inherited from TrackerSamplerAlgorithmTrackerSamplerAlgorithm
   };


TrackerSamplerPF::Params
-------------------------

.. ocv:struct:: TrackerSamplerPF::Params

This structure contains all the parameters that can be varied during the course of sampling algorithm. Below is the structure exposed,
together with its members briefly explained with reference to the above discussion on algorithm's working.

::

   struct CV_EXPORTS Params
   {
    Params();
    int iterationNum; //number of selection rounds
    int particlesNum; //number of "perturbed" boxes on each round
    double alpha; //with each new round we exponentially decrease the amount of "perturbing" we allow (like in simulated annealing)
                  //and this very alpha controls how fast annealing happens, ie. how fast perturbing decreases
    Mat_<double> std; //initial values for perturbing (1-by-4 array, as each rectangle is given by 4 values -- coordinates of opposite vertices,
                      //hence we have 4 values to perturb)
   };
 
TrackerSamplerPF::TrackerSamplerPF
------------------------------------

Constructor

.. ocv:function:: TrackerSamplerPF(const Mat& chosenRect,const TrackerSamplerPF::Params &parameters = TrackerSamplerPF::Params())

    :param chosenRect: Initial rectangle, that is supposed to contain target we'd like to track.
