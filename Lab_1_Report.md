Lab 1 Report
======================================

> Title: Dependency Analysis and Dependency Graph on EnglishPal
>
> Author: 周智豪、顾晓楠、费宇馨、蓝嘉婕
>
> Date: 2021/5/18

Introduction
--------------------------------------

We want to avoid the Big Ball of Mud antipattern in our software application. So we do the dependency analysis and dependency graph for project EnglishPal.

Materials and Methods
--------------------------------------

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
--------------------------------------

* The module-level dependency graph of EnglishPal

  * Dependency dot file

    ```
    strict digraph "dependencies" {
    graph [
         rankdir="LR",
         overlap="scale",
         size="8,10",
         ratio="fill",
         fontsize="16",
         fontname="Helvetica",
         clusterrank="local"
      ]
    
    	node [
        	fontsize=10
        	shape=ellipse
        	// style=filled
        	// shape=box
    	];
    
    "UseSqlite.py"  [style=filled];
    "UseSqlite.py" -> "sqlite3";
    "WordFreq.py"  [style=filled];
    "WordFreq.py" -> "string.py";
    "WordFreq.py" -> "wordfreqCMD.py";
    "difficulty.py"  [style=filled];
    "difficulty.py" -> "math";
    "difficulty.py" -> "pickle.py";
    "difficulty.py" -> "wordfreqCMD.py";
    "main.py"  [style=filled];
    "main.py" -> "datetime";
    "main.py" -> "glob.py";
    "main.py" -> "os.py";
    "main.py" -> "random.py";
    "main.py" -> "UseSqlite.py";
    "main.py" -> "WordFreq.py";
    "main.py" -> "difficulty.py";
    "main.py" -> "pickle_idea.py";
    "main.py" -> "pickle_idea2.py";
    "main.py" -> "wordfreqCMD.py";
    "pickle_idea.py"  [style=filled];
    "pickle_idea.py" -> "pickle.py";
    "pickle_idea2.py"  [style=filled];
    "pickle_idea2.py" -> "datetime";
    "pickle_idea2.py" -> "pickle.py";
    "wordfreqCMD.py"  [style=filled];
    "wordfreqCMD.py" -> "collections.py";
    "wordfreqCMD.py" -> "operator";
    "wordfreqCMD.py" -> "os.py";
    "wordfreqCMD.py" -> "string.py";
    "wordfreqCMD.py" -> "sys";
    "wordfreqCMD.py" -> "pickle_idea.py";
    
    }
    ```
    
  
- Dependency graph
  
  <img src="https://i.bmp.ovh/imgs/2021/05/99029e8097cdedfc.png" alt="dependency graph" style="zoom:100%;" />
  
* Dependency graph in pdf file [snakefood.pdf](https://github.com/Blue-ljj/LanLab1-Report-Docs/blob/master/snakefood.pdf)
  
* The class/function-level dependency graph of EnglishPal

  *  dependency text file 

    ```
    graph LR
    difficulty.load_record --> pickle.load
    difficulty.get_difficulty_level --> difficulty.difficulty_level_from_frequency
    difficulty.user_difficulty_level --> difficulty.revert_dict
    difficulty.user_difficulty_level --> wordfreqCMD.sort_in_ascending_order
    difficulty.text_difficulty_level --> wordfreqCMD.remove_punctuation
    difficulty.text_difficulty_level --> wordfreqCMD.freq
    difficulty.text_difficulty_level --> wordfreqCMD.sort_in_descending_order
    
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

  * Dependency graph rendered by mermaid embedded 

    [![](https://mermaid.ink/img/eyJjb2RlIjoiZ3JhcGggTFJcbmRpZmZpY3VsdHkubG9hZF9yZWNvcmQgLS0-IHBpY2tsZS5sb2FkXG5kaWZmaWN1bHR5LmdldF9kaWZmaWN1bHR5X2xldmVsIC0tPiBkaWZmaWN1bHR5LmRpZmZpY3VsdHlfbGV2ZWxfZnJvbV9mcmVxdWVuY3lcbmRpZmZpY3VsdHkudXNlcl9kaWZmaWN1bHR5X2xldmVsIC0tPiBkaWZmaWN1bHR5LnJldmVydF9kaWN0XG5kaWZmaWN1bHR5LnVzZXJfZGlmZmljdWx0eV9sZXZlbCAtLT4gd29yZGZyZXFDTUQuc29ydF9pbl9hc2NlbmRpbmdfb3JkZXJcbmRpZmZpY3VsdHkudGV4dF9kaWZmaWN1bHR5X2xldmVsIC0tPiB3b3JkZnJlcUNNRC5yZW1vdmVfcHVuY3R1YXRpb25cbmRpZmZpY3VsdHkudGV4dF9kaWZmaWN1bHR5X2xldmVsIC0tPiB3b3JkZnJlcUNNRC5mcmVxXG5kaWZmaWN1bHR5LnRleHRfZGlmZmljdWx0eV9sZXZlbCAtLT4gd29yZGZyZXFDTUQuc29ydF9pbl9kZXNjZW5kaW5nX29yZGVyXG5cbldvcmRGcmVxLl9faW5pdF9fIC0tPiB3b3JkZnJlcUNNRC5yZW1vdmVfcHVuY3R1YXRpb25cbldvcmRGcmVxLl9faW5pdF9fIC0tPiB3b3JkZnJlcUNNRC5mcmVxXG5Xb3JkRnJlcS5nZXRfZnJlcSAtLT4gd29yZGZyZXFDTUQuc29ydF9pbl9kZXNjZW5kaW5nX29yZGVyXG5cbm1haW4ubG9hZF9mcmVxX2hpc3RvcnkgLS0-IHBpY2tsZV9pZGVhLmxvYWRfcmVjb3JkXG5tYWluLnZlcmlmeV91c2VyIC0tPiBSZWNvcmRRdWVyeS5fX2luaXRfX1xubWFpbi52ZXJpZnlfdXNlciAtLT4gUmVjb3JkUXVlcnkuaW5zdHJ1Y3Rpb25zXG5tYWluLnZlcmlmeV91c2VyIC0tPiBSZWNvcmRRdWVyeS5kb1xubWFpbi52ZXJpZnlfdXNlciAtLT4gUmVjb3JkUXVlcnkuZ2V0X3Jlc3VsdHNcblxubWFpbi5hZGRfdXNlciAtLT4gSW5zZXJ0UXVlcnkuX19pbml0X19cbm1haW4uYWRkX3VzZXIgLS0-IEluc2VydFF1ZXJ5Lmluc3RydWN0aW9uc1xubWFpbi5hZGRfdXNlciAtLT4gSW5zZXJ0UXVlcnkuZG9cblxubWFpbi5jaGVja191c2VybmFtZV9hdmFpbGFiaWxpdHkgLS0-IFJlY29yZFF1ZXJ5Ll9faW5pdF9fXG5tYWluLmNoZWNrX3VzZXJuYW1lX2F2YWlsYWJpbGl0eSAtLT4gUmVjb3JkUXVlcnkuaW5zdHJ1Y3Rpb25zXG5tYWluLmNoZWNrX3VzZXJuYW1lX2F2YWlsYWJpbGl0eSAtLT4gUmVjb3JkUXVlcnkuZG9cbm1haW4uY2hlY2tfdXNlcm5hbWVfYXZhaWxhYmlsaXR5IC0tPiBSZWNvcmRRdWVyeS5nZXRfcmVzdWx0c1xuXG5tYWluLmdldF9leHBpcnlfZGF0ZSAtLT4gUmVjb3JkUXVlcnkuX19pbml0X19cbm1haW4uZ2V0X2V4cGlyeV9kYXRlIC0tPiBSZWNvcmRRdWVyeS5pbnN0cnVjdGlvbnNcbm1haW4uZ2V0X2V4cGlyeV9kYXRlIC0tPiBSZWNvcmRRdWVyeS5kb1xubWFpbi5nZXRfZXhwaXJ5X2RhdGUgLS0-IFJlY29yZFF1ZXJ5LmdldF9yZXN1bHRzXG5cbm1haW4uZ2V0X3RvZGF5X2FydGljbGUgLS0-IFJlY29yZFF1ZXJ5Ll9faW5pdF9fXG5tYWluLmdldF90b2RheV9hcnRpY2xlIC0tPiBSZWNvcmRRdWVyeS5pbnN0cnVjdGlvbnNcbm1haW4uZ2V0X3RvZGF5X2FydGljbGUgLS0-IFJlY29yZFF1ZXJ5LmRvXG5tYWluLmdldF90b2RheV9hcnRpY2xlIC0tPiBSZWNvcmRRdWVyeS5nZXRfcmVzdWx0c1xubWFpbi5nZXRfdG9kYXlfYXJ0aWNsZSAtLT4gbWFpbi5sb2FkX2ZyZXFfaGlzdG9yeVxubWFpbi5nZXRfdG9kYXlfYXJ0aWNsZSAtLT4gZGlmZmljdWx0eS5nZXRfZGlmZmljdWx0eV9sZXZlbFxubWFpbi5nZXRfdG9kYXlfYXJ0aWNsZSAtLT4gZGlmZmljdWx0eS50ZXh0X2RpZmZpY3VsdHlfbGV2ZWxcblxubWFpbi5tYXJrX3dvcmQgLS0-IG1haW4ubG9hZF9mcmVxX2hpc3Rvcnlcbm1haW4ubWFya193b3JkIC0tPiBwaWNrbGVfaWRlYS5kaWN0MmxzdFxubWFpbi5tYXJrX3dvcmQgLS0-IHBpY2tsZV9pZGVhLm1lcmdlX2ZyZXF1ZW5jeVxubWFpbi5tYXJrX3dvcmQgLS0-IHBpY2tsZV9pZGVhLnNhdmVfZnJlcXVlbmN5X3RvX3BpY2tsZVxuXG5tYWluLm1haW5wYWdlIC0tPiBXb3JkRnJlcS5fX2luaXRfX1xubWFpbi5tYWlucGFnZSAtLT4gV29yZEZyZXEuZ2V0X2ZyZXFcbm1haW4ubWFpbnBhZ2UgLS0-IG1haW4ubG9hZF9mcmVxX2hpc3Rvcnlcbm1haW4ubWFpbnBhZ2UgLS0-IHBpY2tsZV9pZGVhLmRpY3QybHN0XG5tYWluLm1haW5wYWdlIC0tPiBwaWNrbGVfaWRlYS5tZXJnZV9mcmVxdWVuY3lcbm1haW4ubWFpbnBhZ2UgLS0-IHBpY2tsZV9pZGVhLnNhdmVfZnJlcXVlbmN5X3RvX3BpY2tsZVxubWFpbi5tYWlucGFnZSAtLT4gd29yZGZyZXFDTUQuc29ydF9pbl9kZXNjZW5kaW5nX29yZGVyXG5tYWluLm1haW5wYWdlIC0tPiB3b3JkZnJlcUNNRC55b3VkYW9fbGlua1xuXG5tYWluLnVzZXJfbWFya193b3JkIC0tPiBtYWluLmxvYWRfZnJlcV9oaXN0b3J5XG5tYWluLnVzZXJfbWFya193b3JkIC0tPiBwaWNrbGVfaWRlYTIuZGljdDJsc3Rcbm1haW4udXNlcl9tYXJrX3dvcmQgLS0-IHBpY2tsZV9pZGVhMi5tZXJnZV9mcmVxdWVuY3lcbm1haW4udXNlcl9tYXJrX3dvcmQgLS0-IHBpY2tsZV9pZGVhMi5zYXZlX2ZyZXF1ZW5jeV90b19waWNrbGVcblxubWFpbi51c2VycGFnZSAtLT4gV29yZEZyZXEuX19pbml0X19cbm1haW4udXNlcnBhZ2UgLS0-IFdvcmRGcmVxLmdldF9mcmVxXG5tYWluLnVzZXJwYWdlIC0tPiBwaWNrbGVfaWRlYS5sb2FkX3JlY29yZFxubWFpbi51c2VycGFnZSAtLT4gbWFpbi5nZXRfdG9kYXlfYXJ0aWNsZVxubWFpbi51c2VycGFnZSAtLT4gbWFpbi5sb2FkX2ZyZXFfaGlzdG9yeVxubWFpbi51c2VycGFnZSAtLT4gcGlja2xlX2lkZWEyLmRpY3QybHN0XG5tYWluLnVzZXJwYWdlIC0tPiB3b3JkZnJlcUNNRC55b3VkYW9fbGlua1xuXG5tYWluLnNpZ251cCAtLT4gbWFpbi5hZGRfdXNlclxubWFpbi5zaWdudXAgLS0-IG1haW4udmVyaWZ5X3VzZXJcbm1haW4uc2lnbnVwIC0tPiBtYWluLmNoZWNrX3VzZXJuYW1lX2F2YWlsYWJpbGl0eVxuXG5tYWluLmxvZ2luIC0tPiBtYWluLnZlcmlmeV91c2VyXG5tYWluLmxvZ2luIC0tPiBtYWluLmdldF9leHBpcnlfZGF0ZSIsIm1lcm1haWQiOnt9LCJ1cGRhdGVFZGl0b3IiOmZhbHNlfQ)](https://mermaid-js.github.io/mermaid-live-editor/#/edit/eyJjb2RlIjoiZ3JhcGggTFJcbmRpZmZpY3VsdHkubG9hZF9yZWNvcmQgLS0-IHBpY2tsZS5sb2FkXG5kaWZmaWN1bHR5LmdldF9kaWZmaWN1bHR5X2xldmVsIC0tPiBkaWZmaWN1bHR5LmRpZmZpY3VsdHlfbGV2ZWxfZnJvbV9mcmVxdWVuY3lcbmRpZmZpY3VsdHkudXNlcl9kaWZmaWN1bHR5X2xldmVsIC0tPiBkaWZmaWN1bHR5LnJldmVydF9kaWN0XG5kaWZmaWN1bHR5LnVzZXJfZGlmZmljdWx0eV9sZXZlbCAtLT4gd29yZGZyZXFDTUQuc29ydF9pbl9hc2NlbmRpbmdfb3JkZXJcbmRpZmZpY3VsdHkudGV4dF9kaWZmaWN1bHR5X2xldmVsIC0tPiB3b3JkZnJlcUNNRC5yZW1vdmVfcHVuY3R1YXRpb25cbmRpZmZpY3VsdHkudGV4dF9kaWZmaWN1bHR5X2xldmVsIC0tPiB3b3JkZnJlcUNNRC5mcmVxXG5kaWZmaWN1bHR5LnRleHRfZGlmZmljdWx0eV9sZXZlbCAtLT4gd29yZGZyZXFDTUQuc29ydF9pbl9kZXNjZW5kaW5nX29yZGVyXG5cbldvcmRGcmVxLl9faW5pdF9fIC0tPiB3b3JkZnJlcUNNRC5yZW1vdmVfcHVuY3R1YXRpb25cbldvcmRGcmVxLl9faW5pdF9fIC0tPiB3b3JkZnJlcUNNRC5mcmVxXG5Xb3JkRnJlcS5nZXRfZnJlcSAtLT4gd29yZGZyZXFDTUQuc29ydF9pbl9kZXNjZW5kaW5nX29yZGVyXG5cbm1haW4ubG9hZF9mcmVxX2hpc3RvcnkgLS0-IHBpY2tsZV9pZGVhLmxvYWRfcmVjb3JkXG5tYWluLnZlcmlmeV91c2VyIC0tPiBSZWNvcmRRdWVyeS5fX2luaXRfX1xubWFpbi52ZXJpZnlfdXNlciAtLT4gUmVjb3JkUXVlcnkuaW5zdHJ1Y3Rpb25zXG5tYWluLnZlcmlmeV91c2VyIC0tPiBSZWNvcmRRdWVyeS5kb1xubWFpbi52ZXJpZnlfdXNlciAtLT4gUmVjb3JkUXVlcnkuZ2V0X3Jlc3VsdHNcblxubWFpbi5hZGRfdXNlciAtLT4gSW5zZXJ0UXVlcnkuX19pbml0X19cbm1haW4uYWRkX3VzZXIgLS0-IEluc2VydFF1ZXJ5Lmluc3RydWN0aW9uc1xubWFpbi5hZGRfdXNlciAtLT4gSW5zZXJ0UXVlcnkuZG9cblxubWFpbi5jaGVja191c2VybmFtZV9hdmFpbGFiaWxpdHkgLS0-IFJlY29yZFF1ZXJ5Ll9faW5pdF9fXG5tYWluLmNoZWNrX3VzZXJuYW1lX2F2YWlsYWJpbGl0eSAtLT4gUmVjb3JkUXVlcnkuaW5zdHJ1Y3Rpb25zXG5tYWluLmNoZWNrX3VzZXJuYW1lX2F2YWlsYWJpbGl0eSAtLT4gUmVjb3JkUXVlcnkuZG9cbm1haW4uY2hlY2tfdXNlcm5hbWVfYXZhaWxhYmlsaXR5IC0tPiBSZWNvcmRRdWVyeS5nZXRfcmVzdWx0c1xuXG5tYWluLmdldF9leHBpcnlfZGF0ZSAtLT4gUmVjb3JkUXVlcnkuX19pbml0X19cbm1haW4uZ2V0X2V4cGlyeV9kYXRlIC0tPiBSZWNvcmRRdWVyeS5pbnN0cnVjdGlvbnNcbm1haW4uZ2V0X2V4cGlyeV9kYXRlIC0tPiBSZWNvcmRRdWVyeS5kb1xubWFpbi5nZXRfZXhwaXJ5X2RhdGUgLS0-IFJlY29yZFF1ZXJ5LmdldF9yZXN1bHRzXG5cbm1haW4uZ2V0X3RvZGF5X2FydGljbGUgLS0-IFJlY29yZFF1ZXJ5Ll9faW5pdF9fXG5tYWluLmdldF90b2RheV9hcnRpY2xlIC0tPiBSZWNvcmRRdWVyeS5pbnN0cnVjdGlvbnNcbm1haW4uZ2V0X3RvZGF5X2FydGljbGUgLS0-IFJlY29yZFF1ZXJ5LmRvXG5tYWluLmdldF90b2RheV9hcnRpY2xlIC0tPiBSZWNvcmRRdWVyeS5nZXRfcmVzdWx0c1xubWFpbi5nZXRfdG9kYXlfYXJ0aWNsZSAtLT4gbWFpbi5sb2FkX2ZyZXFfaGlzdG9yeVxubWFpbi5nZXRfdG9kYXlfYXJ0aWNsZSAtLT4gZGlmZmljdWx0eS5nZXRfZGlmZmljdWx0eV9sZXZlbFxubWFpbi5nZXRfdG9kYXlfYXJ0aWNsZSAtLT4gZGlmZmljdWx0eS50ZXh0X2RpZmZpY3VsdHlfbGV2ZWxcblxubWFpbi5tYXJrX3dvcmQgLS0-IG1haW4ubG9hZF9mcmVxX2hpc3Rvcnlcbm1haW4ubWFya193b3JkIC0tPiBwaWNrbGVfaWRlYS5kaWN0MmxzdFxubWFpbi5tYXJrX3dvcmQgLS0-IHBpY2tsZV9pZGVhLm1lcmdlX2ZyZXF1ZW5jeVxubWFpbi5tYXJrX3dvcmQgLS0-IHBpY2tsZV9pZGVhLnNhdmVfZnJlcXVlbmN5X3RvX3BpY2tsZVxuXG5tYWluLm1haW5wYWdlIC0tPiBXb3JkRnJlcS5fX2luaXRfX1xubWFpbi5tYWlucGFnZSAtLT4gV29yZEZyZXEuZ2V0X2ZyZXFcbm1haW4ubWFpbnBhZ2UgLS0-IG1haW4ubG9hZF9mcmVxX2hpc3Rvcnlcbm1haW4ubWFpbnBhZ2UgLS0-IHBpY2tsZV9pZGVhLmRpY3QybHN0XG5tYWluLm1haW5wYWdlIC0tPiBwaWNrbGVfaWRlYS5tZXJnZV9mcmVxdWVuY3lcbm1haW4ubWFpbnBhZ2UgLS0-IHBpY2tsZV9pZGVhLnNhdmVfZnJlcXVlbmN5X3RvX3BpY2tsZVxubWFpbi5tYWlucGFnZSAtLT4gd29yZGZyZXFDTUQuc29ydF9pbl9kZXNjZW5kaW5nX29yZGVyXG5tYWluLm1haW5wYWdlIC0tPiB3b3JkZnJlcUNNRC55b3VkYW9fbGlua1xuXG5tYWluLnVzZXJfbWFya193b3JkIC0tPiBtYWluLmxvYWRfZnJlcV9oaXN0b3J5XG5tYWluLnVzZXJfbWFya193b3JkIC0tPiBwaWNrbGVfaWRlYTIuZGljdDJsc3Rcbm1haW4udXNlcl9tYXJrX3dvcmQgLS0-IHBpY2tsZV9pZGVhMi5tZXJnZV9mcmVxdWVuY3lcbm1haW4udXNlcl9tYXJrX3dvcmQgLS0-IHBpY2tsZV9pZGVhMi5zYXZlX2ZyZXF1ZW5jeV90b19waWNrbGVcblxubWFpbi51c2VycGFnZSAtLT4gV29yZEZyZXEuX19pbml0X19cbm1haW4udXNlcnBhZ2UgLS0-IFdvcmRGcmVxLmdldF9mcmVxXG5tYWluLnVzZXJwYWdlIC0tPiBwaWNrbGVfaWRlYS5sb2FkX3JlY29yZFxubWFpbi51c2VycGFnZSAtLT4gbWFpbi5nZXRfdG9kYXlfYXJ0aWNsZVxubWFpbi51c2VycGFnZSAtLT4gbWFpbi5sb2FkX2ZyZXFfaGlzdG9yeVxubWFpbi51c2VycGFnZSAtLT4gcGlja2xlX2lkZWEyLmRpY3QybHN0XG5tYWluLnVzZXJwYWdlIC0tPiB3b3JkZnJlcUNNRC55b3VkYW9fbGlua1xuXG5tYWluLnNpZ251cCAtLT4gbWFpbi5hZGRfdXNlclxubWFpbi5zaWdudXAgLS0-IG1haW4udmVyaWZ5X3VzZXJcbm1haW4uc2lnbnVwIC0tPiBtYWluLmNoZWNrX3VzZXJuYW1lX2F2YWlsYWJpbGl0eVxuXG5tYWluLmxvZ2luIC0tPiBtYWluLnZlcmlmeV91c2VyXG5tYWluLmxvZ2luIC0tPiBtYWluLmdldF9leHBpcnlfZGF0ZSIsIm1lcm1haWQiOnt9LCJ1cGRhdGVFZGl0b3IiOmZhbHNlfQ)  

    












Pros&Cons
--------------------------------------

- Pros: The project has low complexity and clear framework,so it's easier to maintain the code later.


- Cons: Hinder technological innovation,and there are still many bugs in the project that need to be improved.


Discussions
--------------------------------------

The Snakefood tool can analyze dependencies in project code, but only at the module level. Mermaid can plot the dependency of our input into a function level dependency graph.

Through the dependency analysis and the drawing of the dependency graph of EnglishPal, we can see the framework structure of the project more clearly, which provides convenience for the maintenance and update of the code in the future.

## Read the Docs

Link: [Dependency Analysis and Dependency Graph on EnglishPal](https://lab1-report.readthedocs.io/en/latest/Lab_1_Report.html#)

References
--------------------------------------

[1] [Snakefood User Manual](http://furius.ca/snakefood/doc/snakefood-doc.html)

[2] [Graphviz Online](https://dreampuf.github.io/GraphvizOnline/) 

[3] [A Brief Guide How to Write a Computer Science Lab Report](https://thehackpost.com/a-brief-guide-how-to-write-a-computer-science-lab-report.html)  

[4] [Read the Docs](https://readthedocs.org/ )



[snakefood]:https://github.com/blais/snakefood.git