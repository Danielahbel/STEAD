#  STanford EArthquake Dataset (STEAD):A Global Data Set of Seismic Signals for AI                                                                                                                                                                                                                                                             
![map](map2.png)

![map](stations2.png)

-----------------------------------------                                                                                                                                                                                   
### You can get the wavefoms from here: 

https://rebrand.ly/waveforms-11-13-19  (this is a single hdf5 file ~ 89 GB)

### You can get the metadata from here :
https://rebrand.ly/metadata-11-13-19   (this is a single csv file ~ 350 MB)

* Note: all the metadata are aslo available in the hdf5 file (as attributes associated with each waveform).

### You can get the paper from here:
https://ieeexplore.ieee.org/abstract/document/8871127

### You can send your questions or suggestions to: 
mmousavi@stanford.edu

### Last Update:
April 9, 2020

-------------------------------------
Reference:

`Mousavi, S. M., Sheng, Y., Zhu, W., Beroza G.C., (2019). 
STanford EArthquake Dataset (STEAD): A Global Data Set of Seismic Signals for AI, 
IEEE Access, doi:10.1109/ACCESS.2019.2947848` 


BibTeX:

    @article{mousavi2019stanford,
      title={STanford EArthquake Dataset (STEAD): A Global Data Set of Seismic Signals for AI},
      author={Mousavi, S Mostafa and Sheng, Yixiao and Zhu, Weiqiang and Beroza, Gregory C},
      journal={IEEE Access},
      year={2019},
      publisher={IEEE}
    }

-------------------------------------
### classes and categories in the dataset

    import h5py
    import matplotlib.pyplot as plt
    hdf_f = "STEAD.hdf5"
    r = h5py.File(hdf_f, 'r')

    for n in r:
        print('class: ', n)
        for nn in r[n]:
            print('category: ', nn)

class:  earthquake  
category:  local   
class:  non_earthquake   
category:  noise   

----------
The csv file can be used to easily select specific part of the dataset and only read associated waveforms from the hdf5 file for efficiency.

### Example of data selection and accessing (earthquake waveforms):

        import pandas as pd
        import h5py
        import numpy as np
        import matplotlib.pyplot as plt

        file_name = "waveforms.hdf5"
        csv_file = "metadata.csv"

        # reading the csv file into a dataframe:
        df = pd.read_csv(csv_file)
        print(f'total events in csv file: {len(df)}')
        # filterering the dataframe
        df = df[(df.trace_category == 'earthquake_local') & (df.source_distance_km <= 20) & (df.source_magnitude > 3)]
        print(f'total events selected: {len(df)}')

        # making a list of trace names for the selected data
        ev_list = df['trace_name'].to_list()

        # retrieving selected waveforms from the hdf5 file: 
        dtfl = h5py.File(file_name, 'r')
        for c, evi in enumerate(ev_list):
            dataset = dtfl.get('earthquake/local/'+str(evi)) 
            # waveforms, 3 channels: first row: E channle, second row: N channel, third row: Z channel 
            data = np.array(dataset)

            fig = plt.figure()
            ax = fig.add_subplot(311)         
            plt.plot(data[:,0], 'k')
            plt.rcParams["figure.figsize"] = (8, 5)
            legend_properties = {'weight':'bold'}    
            plt.tight_layout()
            ymin, ymax = ax.get_ylim()
            pl = plt.vlines(dataset.attrs['p_arrival_sample'], ymin, ymax, color='b', linewidth=2, label='P-arrival')
            sl = plt.vlines(dataset.attrs['s_arrival_sample'], ymin, ymax, color='r', linewidth=2, label='S-arrival')
            cl = plt.vlines(dataset.attrs['coda_end_sample'], ymin, ymax, color='aqua', linewidth=2, label='Coda End')
            plt.legend(handles=[pl, sl, cl], loc = 'upper right', borderaxespad=0., prop=legend_properties)        
            plt.ylabel('Amplitude counts', fontsize=12) 
            ax.set_xticklabels([])

            ax = fig.add_subplot(312)         
            plt.plot(data[:,1], 'k')
            plt.rcParams["figure.figsize"] = (8, 5)
            legend_properties = {'weight':'bold'}    
            plt.tight_layout()
            ymin, ymax = ax.get_ylim()
            pl = plt.vlines(dataset.attrs['p_arrival_sample'], ymin, ymax, color='b', linewidth=2, label='P-arrival')
            sl = plt.vlines(dataset.attrs['s_arrival_sample'], ymin, ymax, color='r', linewidth=2, label='S-arrival')
            cl = plt.vlines(dataset.attrs['coda_end_sample'], ymin, ymax, color='aqua', linewidth=2, label='Coda End')
            plt.legend(handles=[pl, sl, cl], loc = 'upper right', borderaxespad=0., prop=legend_properties)        
            plt.ylabel('Amplitude counts', fontsize=12) 
            ax.set_xticklabels([])

            ax = fig.add_subplot(313)         
            plt.plot(data[:,2], 'k')
            plt.rcParams["figure.figsize"] = (8,5)
            legend_properties = {'weight':'bold'}    
            plt.tight_layout()
            ymin, ymax = ax.get_ylim()
            pl = plt.vlines(dataset.attrs['p_arrival_sample'], ymin, ymax, color='b', linewidth=2, label='P-arrival')
            sl = plt.vlines(dataset.attrs['s_arrival_sample'], ymin, ymax, color='r', linewidth=2, label='S-arrival')
            cl = plt.vlines(dataset.attrs['coda_end_sample'], ymin, ymax, color='aqua', linewidth=2, label='Coda End')
            plt.legend(handles=[pl, sl, cl], loc = 'upper right', borderaxespad=0., prop=legend_properties)        
            plt.ylabel('Amplitude counts', fontsize=12) 
            ax.set_xticklabels([])
            plt.show() 

            for at in dataset.attrs:
                print(at, dataset.attrs[at])    

            inp = input("Press a key to plot the next waveform!")
            if inp == "r":
                continue             


![event](eventSample.png)

![event](eventSample2.png)


### Example of data selection and accessing (noise waveforms):
#### Note:
For some of the noise data waveforms are identical for 3 components. These are related to single channel stations.
However, these make up to less than 10 % of noise data. For the rest, noise are different for each channel.

        import pandas as pd
        import h5py
        import numpy as np
        import matplotlib.pyplot as plt

        file_name = "waveforms.hdf5"
        csv_file = "metadata.csv"

        # reading the csv file into a dataframe:
        df = pd.read_csv(csv_file)
        print(f'total events in csv file: {len(df)}')
        # filterering the dataframe
        df = df[(df.trace_category == 'noise') & (df.receiver_code == 'PHOB') ]
        print(f'total events selected: {len(df)}')

        # making a list of trace names for the selected data
        ev_list = df['trace_name'].to_list()[:200]

        # retrieving selected waveforms from the hdf5 file: 
        dtfl = h5py.File(file_name, 'r')
        for c, evi in enumerate(ev_list):
            dataset = dtfl.get('non_earthquake/noise/'+str(evi)) 
            # waveforms, 3 channels: first row: E channle, second row: N channel, third row: Z channel 
            data = np.array(dataset)

            fig = plt.figure()
            ax = fig.add_subplot(311)         
            plt.plot(data[:,0], 'k')
            plt.rcParams["figure.figsize"] = (8, 5)
            legend_properties = {'weight':'bold'}    
            plt.tight_layout()
            plt.ylabel('Amplitude counts', fontsize=12) 
            ax.set_xticklabels([])

            ax = fig.add_subplot(312)         
            plt.plot(data[:,1], 'k')
            plt.rcParams["figure.figsize"] = (8, 5)
            legend_properties = {'weight':'bold'}    
            plt.tight_layout()     
            plt.ylabel('Amplitude counts', fontsize=12) 
            ax.set_xticklabels([])

            ax = fig.add_subplot(313)         
            plt.plot(data[:,2], 'k')
            plt.rcParams["figure.figsize"] = (8,5)
            legend_properties = {'weight':'bold'}    
            plt.tight_layout()     
            plt.ylabel('Amplitude counts', fontsize=12) 
            ax.set_xticklabels([])
            plt.show() 

            for at in dataset.attrs:
                print(at, dataset.attrs[at])    

            inp = input("Press a key to plot the next waveform!")
            if inp == "r":
                continue       

![event](noise.png)

------------------------------------------------------

# See the notebook for the dataset's property                                                                                  
------------------------------------------------------
