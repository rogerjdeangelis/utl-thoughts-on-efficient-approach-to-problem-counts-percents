# utl-thoughts-on-efficient-approach-to-problem-counts-percents
For tiny data like this it probably does not make a big difference how you do it.  
    For tiny data like this it probably does not make a big difference how you do it.                             
                                                                                                                  
    SAS Forum                                                                                                     
    https://tinyurl.com/y2day524                                                                                  
    https://communities.sas.com/t5/SAS-Programming/Thoughts-on-efficient-approach-to-problem/m-p/585820           
                                                                                                                  
    Each study                                                                                                    
                                                                                                                  
    1,500,000 records and 1,231 variables (your average number of observations)                                   
                                                                                                                  
    real time 38.95 seconds (on my 2008 boat anchor computer)                                                     
                                                                                                                  
    Note my dataset has 100 more variables.                                                                       
                                                                                                                  
    The binary compressed dataset is only 3.3gb.                                                                  
                                                                                                                  
    I you have the AMD 32 core workstation you could run  30 tasks simultaneously.                                
    Whn mutitasking conside using SAS file to miminize I/O conflicks.                                             
                                                                                                                  
    The missing values are not used in 'proc summary' statistics.                                                 
    The mean is also the percent (just print with a percent format.                                               
                                                                                                                  
    *_                   _                                                                                        
    (_)_ __  _ __  _   _| |_                                                                                      
    | | '_ \| '_ \| | | | __|                                                                                     
    | | | | | |_) | |_| | |_                                                                                      
    |_|_| |_| .__/ \__,_|\__|                                                                                     
            |_|                                                                                                   
    ;                                                                                                             
                                                                                                                  
    options nonotes;                                                                                              
    options compress=binary;                                                                                      
    data have;                                                                                                    
    retain s1 "s1" person;                                                                                        
    call streaminit(54321);                                                                                       
    array ds[1231] d1-d1231;                                                                                      
     do person=1 to 1500000;                                                                                      
        do i=1 to 1231;                                                                                           
           ds[i]= sqrt(rand("Table", 0.5, 0.2, 0.3)-2);                                                           
        end;                                                                                                      
      output;                                                                                                     
     end;                                                                                                         
     drop i;                                                                                                      
    run;quit;                                                                                                     
    options notes;                                                                                                
                                                                                                                  
                                                                                                                  
    Middle Observation(750,000 ) of have - Total Obs 1,500,000                                                    
                                                                                                                  
     -- CHARACTER --                                                                                              
    S1                  C2       Values                                                                           
                                                                                                                  
     -- NUMERIC --                                                                                                
    PERSON              N8       750000                                                                           
    D1                  N8       .                                                                                
    D2                  N8       .                                                                                
    D3                  N8       1                                                                                
    D4                  N8       .                                                                                
    D5                  N8       0                                                                                
    ...                                                                                                           
    D1227               N8       .                                                                                
    D1228               N8       1                                                                                
    D1229               N8       .                                                                                
    D1230               N8       0                                                                                
    D1231               N8       1                                                                                
                                                                                                                  
    *            _               _                                                                                
      ___  _   _| |_ _ __  _   _| |_                                                                              
     / _ \| | | | __| '_ \| | | | __|                                                                             
    | (_) | |_| | |_| |_) | |_| | |_                                                                              
     \___/ \__,_|\__| .__/ \__,_|\__|                                                                             
                    |_|                                                                                           
    ;                                                                                                             
                                                                     D1231_    D1231_                             
     _FREQ_       D1_N     D1_SUM    D1_MEAN             D1231_N      SUM       MEAN                              
                                             449833                                                               
    1,500,000    749740    449833    0.59999 = ------     749995    450068    0.60009                             
                                             749740                                                               
                                                                                                                  
    proc summary data=have mean;                                                                                  
    var _numeric_;                                                                                                
    output out=want mean= n= sum=/ autoname;                                                                      
    run;quit;                                                                                                     
                                                                                                                  
                                                                                                                  
    NOTE: There were 1500000 observations read from the data set WORK.HAVE.                                       
    NOTE: The data set WORK.WANT has 1 observations and 2468 variables.                                           
    NOTE: Compressing data set WORK.WANT increased size by 25.00 percent.                                         
          Compressed is 5 pages; un-compressed would require 4 pages.                                             
    NOTE: PROCEDURE SUMMARY used (Total process time):                                                            
          real time           38.95 seconds                                                                       
          user cpu time       1:22.40                                                                             
          system cpu time     6.42 seconds                                                                        
          memory              11458.46k                                                                           
          OS Memory           37164.00k                                                                           
          Timestamp           09/03/2019 07:47:05 AM                                                              
          Step Count          229  Switch Count  0                                                                
                                                                                                                  
                                                                                                                  
    proc print data=want wirth=min;                                                                               
    var                                                                                                           
       _freq_                                                                                                     
       D1_N                                                                                                       
       D1_SUM                                                                                                     
       D1_MEAN                                                                                                    
       D1231_N                                                                                                    
       D1231_SUM                                                                                                  
       D1231_MEAN                                                                                                 
    ;                                                                                                             
    run;quit;                                                                                                     
                                                                                                                  
