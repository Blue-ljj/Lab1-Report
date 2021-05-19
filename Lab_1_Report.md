Lab 1 Report
======================================

> Title: Dependency Analysis and Dependency Graph on EnglishPal
>
> Author: 周智豪、顾晓楠、费宇馨、蓝嘉婕
>
> Date: 2021/5/18

Introduction
======================================

We want to avoid the Big Ball of Mud antipattern in our software application. So we do the dependency analysis and dependency graph for project EnglishPal.

Materials and Methods
======================================

EnglishPal is based on python. In this experiment, we do dependency analysis and draw dependency graph about it. We use Snakefood[1] tool to generate module level dependency graph, and use Mermaid tool to draw class / function level dependency graph.

> Operating system : Ubuntu 18.04.5 LTS
>
> Python version : 2.7
>
> Snakefood git repository : [snakefood][snakefood]

- Generating dot file for the module-level dependency graph 

  ```bash
  sfood EnglishPal/app | sfood-graph > snakefood.dot
  ```

- Generating pdf file for the module-level dependency graph

  ```bash
  sfood EnglishPal/app | sfood-graph | dot -Tps | ps2pdf - snakefood.pdf
  # install related packages before generating the pdf file, such as graphviz and ghostscript
  ```

Results
======================================

* The module-level dependency graph of EnglishPal

  * Dependency dot file

    > strict digraph "dependencies" {
    >     graph [
    >            rankdir="LR",
    >            overlap="scale",
    >            size="8,10",
    >            ratio="fill",
    >            fontsize="16",
    >            fontname="Helvetica",
    >            clusterrank="local"
    >         ]
    >
    > ​	node [
    > ​       	fontsize=10
    > ​       	shape=ellipse
    > ​       	// style=filled
    > ​       	// shape=box
    >    	];
    >
    > "UseSqlite.py"  [style=filled];
    > "UseSqlite.py" -> "sqlite3";
    > "WordFreq.py"  [style=filled];
    > "WordFreq.py" -> "string.py";
    > "WordFreq.py" -> "wordfreqCMD.py";
    > "difficulty.py"  [style=filled];
    > "difficulty.py" -> "math";
    > "difficulty.py" -> "pickle.py";
    > "difficulty.py" -> "wordfreqCMD.py";
    > "main.py"  [style=filled];
    > "main.py" -> "datetime";
    > "main.py" -> "glob.py";
    > "main.py" -> "os.py";
    > "main.py" -> "random.py";
    > "main.py" -> "UseSqlite.py";
    > "main.py" -> "WordFreq.py";
    > "main.py" -> "difficulty.py";
    > "main.py" -> "pickle_idea.py";
    > "main.py" -> "pickle_idea2.py";
    > "main.py" -> "wordfreqCMD.py";
    > "pickle_idea.py"  [style=filled];
    > "pickle_idea.py" -> "pickle.py";
    > "pickle_idea2.py"  [style=filled];
    > "pickle_idea2.py" -> "datetime";
    > "pickle_idea2.py" -> "pickle.py";
    > "wordfreqCMD.py"  [style=filled];
    > "wordfreqCMD.py" -> "collections.py";
    > "wordfreqCMD.py" -> "operator";
    > "wordfreqCMD.py" -> "os.py";
    > "wordfreqCMD.py" -> "string.py";
    > "wordfreqCMD.py" -> "sys";
    > "wordfreqCMD.py" -> "pickle_idea.py";
    >
    > }

  - Dependency graph

    <img src="https://i.imgur.com/iCvMF8M.png" alt="dependency graph" style="zoom:50%;" />

  * Dependency graph in pdf file [snakefood.pdf](https://github.com/Blue-ljj/LanLab1-Report-Docs/blob/master/snakefood.pdf)

* The class/function-level dependency graph of EnglishPal

  *  dependency text file 
  
    > ```
    > graph LR
    > difficulty.load_record --> pickle.load
    > difficulty.get_difficulty_level --> difficulty.difficulty_level_from_frequency
    > difficulty.user_difficulty_level --> difficulty.revert_dict
    > difficulty.user_difficulty_level --> wordfreqCMD.sort_in_ascending_order
    > difficulty.text_difficulty_level --> wordfreqCMD.remove_punctuation
    > difficulty.text_difficulty_level --> wordfreqCMD.freq
    > difficulty.text_difficulty_level --> wordfreqCMD.sort_in_descending_order
    > 
    > WordFreq.__init__ --> wordfreqCMD.remove_punctuation
    > WordFreq.__init__ --> wordfreqCMD.freq
    > WordFreq.get_freq --> wordfreqCMD.sort_in_descending_order
    > 
    > main.load_freq_history --> pickle_idea.load_record
    > main.verify_user --> RecordQuery.__init__
    > main.verify_user --> RecordQuery.instructions
    > main.verify_user --> RecordQuery.do
    > main.verify_user --> RecordQuery.get_results
    > 
    > main.add_user --> InsertQuery.__init__
    > main.add_user --> InsertQuery.instructions
    > main.add_user --> InsertQuery.do
    > 
    > main.check_username_availability --> RecordQuery.__init__
    > main.check_username_availability --> RecordQuery.instructions
    > main.check_username_availability --> RecordQuery.do
    > main.check_username_availability --> RecordQuery.get_results
    > 
    > main.get_expiry_date --> RecordQuery.__init__
    > main.get_expiry_date --> RecordQuery.instructions
    > main.get_expiry_date --> RecordQuery.do
    > main.get_expiry_date --> RecordQuery.get_results
    > 
    > main.get_today_article --> RecordQuery.__init__
    > main.get_today_article --> RecordQuery.instructions
    > main.get_today_article --> RecordQuery.do
    > main.get_today_article --> RecordQuery.get_results
    > main.get_today_article --> main.load_freq_history
    > main.get_today_article --> difficulty.get_difficulty_level
    > main.get_today_article --> difficulty.text_difficulty_level
    > 
    > main.mark_word --> main.load_freq_history
    > main.mark_word --> pickle_idea.dict2lst
    > main.mark_word --> pickle_idea.merge_frequency
    > main.mark_word --> pickle_idea.save_frequency_to_pickle
    > 
    > main.mainpage --> WordFreq.__init__
    > main.mainpage --> WordFreq.get_freq
    > main.mainpage --> main.load_freq_history
    > main.mainpage --> pickle_idea.dict2lst
    > main.mainpage --> pickle_idea.merge_frequency
    > main.mainpage --> pickle_idea.save_frequency_to_pickle
    > main.mainpage --> wordfreqCMD.sort_in_descending_order
    > main.mainpage --> wordfreqCMD.youdao_link
    > 
    > main.user_mark_word --> main.load_freq_history
    > main.user_mark_word --> pickle_idea2.dict2lst
    > main.user_mark_word --> pickle_idea2.merge_frequency
    > main.user_mark_word --> pickle_idea2.save_frequency_to_pickle
    > 
    > main.userpage --> WordFreq.__init__
    > main.userpage --> WordFreq.get_freq
    > main.userpage --> pickle_idea.load_record
    > main.userpage --> main.get_today_article
    > main.userpage --> main.load_freq_history
    > main.userpage --> pickle_idea2.dict2lst
    > main.userpage --> wordfreqCMD.youdao_link
    > 
    > main.signup --> main.add_user
    > main.signup --> main.verify_user
    > main.signup --> main.check_username_availability
    > 
    > main.login --> main.verify_user
    > main.login --> main.get_expiry_date
    > ```
  
  * Dependency graph rendered by mermaid embedded  in Typora  
  
    ```mermaid
    graph LR
    difficulty.load_record --> pickle.load
    
    difficulty.get_difficulty_level --> difficulty.difficulty_level_from_frequency
    difficulty.user_difficulty_level --> difficulty.revert_dict
    difficulty.user_difficulty_level --> wordfreqCMD.sort_in_ascending_order
    difficulty.text_difficulty_level --> wordfreqCMD.remove_punctuation
    difficulty.text_difficulty_level --> wordfreqCMD.freq
    difficulty.text_difficulty_level --> wordfreqCMD.sort_in_descending_order
    
    
    #UseSqlite.Sqlite3Template
    
    WordFreq.__init__ --> wordfreqCMD.remove_punctuation
    WordFreq.__init__ --> wordfreqCMD.freq
    WordFreq.get_freq --> wordfreqCMD.sort_in_descending_order
    
    main.load_freq_history --> pickle_idea.load_record
    main.verify_user --> RecordQuery.__init__
    main.verify_user --> RecordQuery.instructions
    main.verify_user --> RecordQuery.do
    main.verify_user --> RecordQuery.get_results
    main.add_user --> InsertQuery.__init__
    main.add_user --> InsertQuery.instructions
    main.add_user --> InsertQuery.do
    
    
    
    main.check_username_availability --> RecordQuery.__init__
    main.check_username_availability --> RecordQuery.instructions
    main.check_username_availability --> RecordQuery.do
    main.check_username_availability --> RecordQuery.get_results
    
    main.get_expiry_date --> RecordQuery.__init__
    main.get_expiry_date --> RecordQuery.instructions
    main.get_expiry_date --> RecordQuery.do
    main.get_expiry_date --> RecordQuery.get_results
    
    main.get_today_article --> RecordQuery.__init__
    main.get_today_article --> RecordQuery.instructions
    main.get_today_article --> RecordQuery.do
    main.get_today_article --> RecordQuery.get_results
    main.get_today_article --> main.load_freq_history
    main.get_today_article --> difficulty.get_difficulty_level
    main.get_today_article --> difficulty.text_difficulty_level
    
    main.mark_word --> main.load_freq_history
    main.mark_word --> pickle_idea.dict2lst
    main.mark_word --> pickle_idea.merge_frequency
    main.mark_word --> pickle_idea.save_frequency_to_pickle
    
    main.mainpage --> WordFreq.__init__
    main.mainpage --> WordFreq.get_freq
    main.mainpage --> main.load_freq_history
    main.mainpage --> pickle_idea.dict2lst
    main.mainpage --> pickle_idea.merge_frequency
    main.mainpage --> pickle_idea.save_frequency_to_pickle
    main.mainpage --> wordfreqCMD.sort_in_descending_order
    main.mainpage --> wordfreqCMD.youdao_link
    
    
    main.user_mark_word --> main.load_freq_history
    main.user_mark_word --> pickle_idea2.dict2lst
    main.user_mark_word --> pickle_idea2.merge_frequency
    main.user_mark_word --> pickle_idea2.save_frequency_to_pickle
    
    main.userpage --> WordFreq.__init__
    main.userpage --> WordFreq.get_freq
    main.userpage --> pickle_idea.load_record
    main.userpage --> main.get_today_article
    main.userpage --> main.load_freq_history
    main.userpage --> pickle_idea2.dict2lst
    main.userpage --> wordfreqCMD.youdao_link
    
    main.signup --> main.add_user
    main.signup --> main.verify_user
    main.signup --> main.check_username_availability
    
    main.login --> main.verify_user
    main.login --> main.get_expiry_date
    ```

Pros&Cons
======================================

- Pros: The project has low complexity and clear framework,so it's easier to maintain the code later.


- Cons: Hinder technological innovation,and there are still many bugs in the project that need to be improved.


Discussions
======================================

The Snakefood tool can analyze dependencies in project code, but only at the module level. Mermaid can plot the dependency of our input into a function level dependency graph.

Through the dependency analysis and the drawing of the dependency graph of EnglishPal, we can see the framework structure of the project more clearly, which provides convenience for the maintenance and update of the code in the future.

References
======================================

[1] [Snakefood User Manual](http://furius.ca/snakefood/doc/snakefood-doc.html)

[2] [Graphviz Online](https://dreampuf.github.io/GraphvizOnline/) 

[3] [A Brief Guide How to Write a Computer Science Lab Report](https://thehackpost.com/a-brief-guide-how-to-write-a-computer-science-lab-report.html)  

[4] [Read the Docs](https://readthedocs.org/ )



[snakefood]:https://github.com/blais/snakefood.git