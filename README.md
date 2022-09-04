# iaafr

# Improved apriori algorithm for recommendation
        import numpy as np
        import pandas as pd
        import itertools
        df=pd.read_csv("ntccdataset12yrs.csv")
        df=df/5
        min_support_threshold=0.15
        df['Year']=df['Year']*5
        max_membership_1=10
        for i in df.columns:
                if len(i.split(' '))!=1:

     # print(i.split(' ')[0]+i.split(' ')[1].zfill(2))
        old_column=i
        new_column=i.split(' ')[0]+i.split(' ')[1].zfill(2)
    # print(old_column,new_column)
        df=df.rename(columns={old_column:new_column})
    
# print(pd.Series(feature_combination))   
    features=df.columns[1:]
    feature_combination=[]
    for i in np.arange(0,max_membership_1):
        feature_combination=feature_combination+list(itertools.combinations(features,i+1))
    print(len(feature_combination))


    df_summary=dict()
    for combination in feature_combination:
        df_summary[combination]=df[list(combination)].min(axis=1).sum()/df.shape[0]
    
    df_summary_1=dict()
    for key, value in df_summary.items():
        if value>=min_support_threshold:
                df_summary_1[key]=value


# pd.set_option('display.width', 10000000)
    value_list=[]
    key_name_list=[]
    for key, value in df_summary.items():
        key_name=''
        for k in key:
            key_name=key_name+k+ " ,"
        key_name=key_name[:len(key_name)-1]
    
        key_name_list.append(key_name)
        value_list.append(value)
    # df_support_values_
        df_support_values_=pd.DataFrame({'item_sets':key_name_list,'support_value':value_list})
        df_support_values_['support_value_threshold_met']=np.where(df_support_values_['support_value']>=min_support_threshold,
                                                          1,0)
        df_support_values_.to_csv('support_values.csv',index=False)





# Distribution of Item Sets to get the estimate the optimal support threshold
    pd.Series(df_summary.values()).hist(bins=20)

# 1. Support values of all the itemsets
    pd.set_option('display.max_rows',2000)
    pd.options.display.max_colwidth = 1000
    df_support_values_

# 2. List of Item Sets above the threshold value
    df_support_values_.query('support_value_threshold_met==1').reset_index().drop('index',1)
    df_summary_2=dict()
    confidence_x_to_y=dict()
    lift_x_to_y=dict()
    for value1 in df_summary_1.keys():
    for value2 in df_summary_1.keys():
        if value1==value2:
            pass
        else:
    #       print(type(value1))
    #       print(value1,value2,tuple(list(value1)+list(value2)))
            key_value_1=tuple(set(sorted(list(value1)+list(value2))))
            key_value_1=tuple(sorted(key_value_1))
    #       print(key_value_1)
            arrow_name=str(list(value1))+' -> '+str(list(value2))

            numerator_fuzzy_intersection=df_summary[key_value_1]
            denominator_fuzzy_intersection=df_summary[value1]
            denominator_lift_intersection=df_summary[value2]
            confidence_x_to_y[arrow_name]=numerator_fuzzy_intersection/denominator_fuzzy_intersection



# Distribution of Confidence of itemsets to get optimal Confidence threshold
    pd.Series(confidence_x_to_y.values()).hist(bins=20)



# pd.set_option('display.width', 10000000)
    min_confidence_threshold=0.5
    value_list=[]
    key_name_list=[]
    for key, value in confidence_x_to_y.items():
    #     key_name=''
    #     for k in key:
    #         key_name=key_name+k+ " ,"
    #     key_name=key_name[:len(key_name)-1]
    
        key_name_list.append(key)
        value_list.append(value)
    #df_support_values_
        df_confidence_values_=pd.DataFrame({'confidence_item_1_to_item_2':key_name_list,'confidence_value':value_list})
    df_confidence_values_['confidence_value_threshold_met']=np.where(df_confidence_values_['confidence_value']>=min_confidence_threshold,
                                                          1,0)
    df_confidence_values_.to_csv('confidence_value.csv',index=False)





#   3. Confidence values of all the itemsets (displaying only randomly sampled 25 as there are 78K combinations possible)
    pd.set_option('display.max_rows',2000)
    pd.options.display.max_colwidth = 1000
    df_confidence_values_.sample(25).reset_index().drop('index',1)




#   4.List of Item Sets Above Confidence Threshold Value (displaying only randomly sampled 25 as there are 69K combinations possible)
    pd.set_option('display.max_rows',2000)
    pd.options.display.max_colwidth = 1000
    df_confidence_values_.query('confidence_value_threshold_met==1').sample(25).reset_index().drop('index',1)

    df_summary_2=dict()
    # confidence_x_to_y=dict()
    lift_x_to_y=dict()
    for value1 in df_summary_1.keys():
        for value2 in df_summary_1.keys():
                if value1==value2:
                        pass
                else:

                        key_value_1=tuple(set(sorted(list(value1)+list(value2))))
                        key_value_1=tuple(sorted(key_value_1))
                        arrow_name=str(list(value1))+' -> '+str(list(value2))
                        numerator_fuzzy_intersection=df_summary[key_value_1]
                        denominator_fuzzy_intersection=df_summary[value1]
                        denominator_lift_intersection=df_summary[value2]
                        if (numerator_fuzzy_intersection/denominator_fuzzy_intersection)>=min_confidence_threshold:
                lift_x_to_y[arrow_name]=numerator_fuzzy_intersection/(denominator_fuzzy_intersection*denominator_lift_intersection)
        
        
    # pd.set_option('display.width', 10000000)
    min_confidence_threshold=0.5
    value_list=[]
    key_name_list=[]
    for key, value in lift_x_to_y.items():    
        key_name_list.append(key)
        value_list.append(value)
    # df_support_values_       
    df_lift_x_to_y=pd.DataFrame({'lift_item_1_to_item_2':key_name_list,'lift_value':value_list})
    df_lift_x_to_y.to_csv('lift_x_to_y.csv',index=False)
   



# 5.Lift Values(displaying only randomly sampled 25 as there are 69K combinations possible)

    pd.set_option('display.max_rows',2000)
    pd.options.display.max_colwidth = 1000
    df_lift_x_to_y.sample(25).reset_index().drop('index',1)



[ntccdataset12yrs.csv](https://github.com/rhustle8/iaafr/files/9484884/ntccdataset12yrs.csv)
