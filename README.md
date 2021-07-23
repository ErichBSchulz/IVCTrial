# IVCTrial

This is set of notebooks for doing power calculations based on simulated 
student success as a repeated binary outcome.

def run_test(testing = True, phase=0):
    pairs = {'none': ['control','nodif'],
                 'vsmall': ['control','interventionA'],
                 'small': ['control','interventionB'],
                 'mod': ['control','interventionBa'],
                 'mod1': ['control','interventionBb'],
                 'big': ['control', 'interventionC'],
                 'huge': ['control', 'interventionD'],
                 'upper': [ 'interventionC', 'interventionD']
                }
    if testing:
        students_per_group = [5]
        attempts_per_student = [5]
        trials_to_test = 3
        cache = "cache_test/"
    else:
        students_per_group = [30] if phase == 0 else [30, 75, 100] if phase == 1 else [20, 30, 50, 75, 100, 200, 400]
        attempts_per_student = [10] if phase < 2 else [10,20]
        trials_to_test = trials['uniform']['ad'].trial.max()+1
        cache="cache2/"

    print(f"testing {trials_to_test} trials")
    syms = [] # temporyary value replaced with a dataframe
    for student_set, trial in trials.items():
        ad = trial['ad']
        sym = run_sym(
            student_set = student_set,
            attempt_data = ad,
            pairs = pairs,
            run_key="d", tests = ['geeBIs'],
            trials_to_test = trials_to_test,
            spg = students_per_group,
            aps = attempts_per_student,
            #run_key="c",   tests = ['geeB','geeBI','geeBIs']
            cache_path=cache)
        # accumulate all the results into a single df
        syms = syms.append(sym) if len(syms) else sym
    return syms
def explain_pairs(pairs, trials):
    x = []
    for student_set, trial in trials.items():  
        d = trial['ad'][['group','result']].groupby('group').agg(['mean', 'sum', 'count'])
        for name, pair in pairs.items():
            m = [d.loc[pair[g]].result["mean"].real * 100 for g in range(2)]
            x1 = {"student_set": student_set, "pair": name, "ga": pair[0], "gb": pair[1], "a": m[0], "b": m[1], "dif": m[1] - m[0]}
            x.append(x1)
    return pd.DataFrame(x)
        

pairs = {'none': ['control','nodif'],
                 'vsmall': ['control','interventionA'],
                 'small': ['control','interventionB'],
                 'mod': ['control','interventionBa'],
                 'mod1': ['control','interventionBb'],         
                 'big': ['control', 'interventionC'],
                 'huge': ['control', 'interventionD'],
                 'upper': [ 'interventionC', 'interventionD']
                 }
explain_pairs(pairs, trials).to_csv("downstairs_pairs.csv")