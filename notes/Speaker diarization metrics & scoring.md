Similar information on SD metrics is also present in [[A Review of Speaker Diarization#1.4 Diarization evaluation metrics|this]] article.

# **1. Metrics**
## ➤ Diarization error rate
Following tradition in this area, we report diarization error rate (DER), which is the sum of:
- speaker error→ percentage of scored time for which the wrong speaker id is assigned within a speech region
- false alarm speech → percentage of scored time for which a nonspeech region is incorrectly marked as containing speech
- missed speech → percentage of scored time for which a speech region is incorrectly marked as not containing speech

As with word error rate, a score of zero indicates perfect performance and higher scores (which may exceed 100) indicate poorer performance. For more details, consult section 6.1 of the [NIST RT-09 evaluation plan](https://web.archive.org/web/20100606041157if_/http://www.itl.nist.gov/iad/mig/tests/rt/2009/docs/rt09-meeting-eval-plan-v2.pdf).


## ➤ Jaccard error rate
We also report Jaccard error rate (JER), a metric introduced for [DIHARD II](https://coml.lscp.ens.fr/dihard/index.html) that is based on the [Jaccard index](https://en.wikipedia.org/wiki/Jaccard_index). The Jaccard index is a similarity measure typically used to evaluate the output of image segmentation systems and is defined as the ratio between the intersection and union of two segmentations. To compute Jaccard error rate, an optimal mapping between reference and system speakers is determined and for each pair the Jaccard index of their segmentations is computed. The Jaccard error rate is then 1 minus the average of these scores.

More concretely, assume we have `N` reference speakers and `M` system speakers. An optimal mapping between speakers is determined using the Hungarian algorithm so that each reference speaker is paired with at most one system speaker and each system speaker with at most one reference speaker. Then, for each reference speaker `ref` the speaker-specific Jaccard error rate is `(FA + MISS)/TOTAL`, where:

- `TOTAL` is the duration of the union of reference and system speaker segments; if the reference speaker was not paired with a system speaker, it is the duration of all reference speaker segments
-  `FA` is the total system speaker time not attributed to the reference speaker; if the reference speaker was not paired with a system speaker, it is 0
-  `MISS` is the total reference speaker time not attributed to the system speaker; if the reference speaker was not paired with a system speaker, it is equal to `TOTAL`

The Jaccard error rate then is the average of the speaker specific Jaccard error rates.

JER and DER are highly correlated with JER typically being higher, especially in recordings where one or more speakers is particularly dominant. Where it tends to track DER is in outliers where the diarization is especially bad, resulting in one or more unmapped system speakers whose speech is not then penalized. In these cases, where DER can easily exceed 500%, JER will never exceed 100% and may be far lower if the reference speakers are handled correctly. For this reason, it may be useful to pair JER with another metric evaluating speech detection and/or speaker overlap detection.


## ➤ Clustering metrics

A third approach to system evaluation is convert both the reference and system outputs to frame-level labels, then evaluate using one of many well-known approaches for evaluating clustering performance. Each recording is converted to a sequence of 10 ms frames, each of which is assigned a single label corresponding to one of the following cases:

-  the frame contains no speech
-  the frame contains speech from a single speaker (one label per speaker indentified)
-  the frame contains overlapping speech (one label for each element in the powerset of speakers)

These frame-level labelings are then scored with the following metrics:


### Goodman-Kruskal tau
Goodman-Kruskal tau is an asymmetric association measure dating back to work by Leo Goodman and William Kruskal in the 1950s (Goodman and Kruskal, 1954). For a reference labeling `ref` and a system labeling `sys`, `GKT(ref, sys)` corresponds to the fraction of variability in `sys` that can be explained by `ref`. Consequently, `GKT(ref, sys)` is 1 when `ref` is perfectly predictive of `sys` and 0 when it is not predictive at all. Correspondingly, `GKT(sys, ref)` is 1 when `sys` is perfectly predictive of `ref` and 0 when lacking any predictive power.

### B-cubed precision, recall, and F1
The B-cubed precision for a single frame assigned speaker `S` in the reference diarization and `C` in the system diarization is the proportion of frames assigned `C` that are also assigned `S`. Similarly, the B-cubed recall for a frame is the proportion of all frames assigned `S` that are also assigned `C`. The overall precision and recall, then, are just the mean of the frame-level precision and recall measures and the overall F-1 their harmonic mean. For additional details see Bagga and Baldwin (1998).

### Information theoretic measures
We report four information theoretic measures:
-   `H(ref|sys)` -- conditional conditional entropy in bits of the reference labeling given the system labeling
-   `H(sys|ref)` -- conditional conditional entropy in bits of the system labeling given the reference labeling
-   `MI` -- mutual information in bits between the reference and system labelings
-   `NMI` -- normalized mutual information between the reference and system labelings; that is, `MI` scaled to the interval [0, 1]. In this case, the normalization term used is `sqrt(H(ref)*H(sys))`.

`H(ref|sys)` is the number of bits needed to describe the reference labeling given that the system labeling is known and ranges from 0 in the case that the system labeling is perfectly predictive of the reference labeling to `H(ref)` in the case that the system labeling is not at all predictive of the reference labeling. Similarly, `H(sys|ref)` measure the number of bits required to describe the system labeling given the reference labeling and ranges from 0 to `H(sys)`.

`MI` is the number of bits shared by the reference and system labeling and indicates the degree to which knowing either reduces uncertainty in the other. It is related to conditional entropy and entropy as follows: `MI(ref, sys) = H(ref) - H(ref|sys) = H(sys) - H(sys|ref)`. `NMI` is derived from `MI` by normalizing it to the interval [0, 1]. Multiple normalizations are possible depending on the upper-bound for `MI` that is used, but we report `NMI` normalized by `sqrt(H(ref)*H(sys))`.


# **2. Scoring**
To evaluate system output stored in [RTTM](https://github.com/nryant/dscore#rttm) files `sys1.rttm`, `sys2.rttm`, ... against a corresponding reference diarization stored in RTTM files `ref1.rttm`, `ref2.rttm`, ...:

```
python score.py -r ref1.rttm ref2.rttm ... -s sys1.rttm sys2.rttm ...
```

which will calculate and report the following metrics both overall and on a per-file basis:

-   `DER` -- diarization error rate (in percent)
-   `JER` -- Jaccard error rate (in percent)
-   `B3-Precision` -- B-cubed precision
-   `B3-Recall` -- B-cubed recall
-   `B3-F1` -- B-cubed F1
-   `GKT(ref, sys)` -- Goodman-Kruskal tau in the direction of the reference diarization to the system diarization
-   `GKT(sys, ref)` -- Goodman-Kruskal tau in the direction of the system diarization to the reference diarization
-   `H(ref|sys)` -- conditional entropy in bits of the reference diarization given the system diarization
-   `H(sys|ref)` -- conditional entropy in bits of the system diarization given the reference diarization
-   `MI` -- mutual information in bits
-   `NMI` -- normalized mutual information

Alternately, we could have specified the reference and system RTTM files via script files of paths (one per line) using the `-R` and `-S` flags:

```
python score.py -R ref.scp -S sys.scp
```

By default the scoring regions for each file will be determined automatically from the reference and speaker turns. However, it is possible to specify explicit scoring regions using a NIST [un-partitioned evaluation map (UEM)](https://github.com/nryant/dscore#uem) file and the `-u` flag. For instance, the following:

```
python score.py -u all.uem -R ref.scp -S sys.scp
```

will load the files to be scored plus scoring regions from `all.uem`, filter out and warn about any speaker turns not present in those files, and trim the remaining turns to the relevant scoring regions before computing the metrics as before.

DER is scored using the NIST `md-eval.pl` tool with a default collar size of 0 ms and explicitly including regions that contain overlapping speech in the reference diarization. If desired, this behavior can be altered using the `--collar` and `--ignore_overlaps` flags. For instance

```
python score.py --collar 0.100 --ignore_overlaps -R ref.scp -S sys.scp
```

would compute DER using a 100 ms collar and with overlapped speech ignored. All other metrics are computed off of frame-level labelings generated from the reference and system speaker turns **WITHOUT** any use of collars. The default frame step is 10 ms, which may be altered via the `--step` flag. For more details, consult the docstrings within the `scorelib.metrics` module.

The overall and per-file results will be printed to STDOUT as a table; for instance:

```
File                           DER    JER    B3-Precision    B3-Recall    B3-F1    GKT(ref, sys)    GKT(sys, ref)    H(ref|sys)    H(sys|ref)    MI    NMI
---------------------------  -----  -----  --------------  -----------  -------  ---------------  ---------------  ------------  ------------  ----  -----
CMU_20020319-1400_d01_NONE    6.10  20.10            0.91         1.00     0.95             1.00             0.88          0.22          0.00  2.66   0.96
ICSI_20000807-1000_d05_NONE  17.37  21.92            0.72         1.00     0.84             1.00             0.68          0.65          0.00  2.79   0.90
ICSI_20011030-1030_d02_NONE  13.06  25.61            0.80         0.95     0.87             0.95             0.80          0.54          0.11  5.10   0.94
LDC_20011116-1400_d06_NONE    5.64  16.10            0.95         0.89     0.92             0.85             0.93          0.10          0.27  1.87   0.91
LDC_20011116-1500_d07_NONE    1.69   2.00            0.96         0.96     0.96             0.95             0.95          0.14          0.12  2.39   0.95
NIST_20020305-1007_d01_NONE  42.05  53.38            0.51         0.95     0.66             0.93             0.44          1.58          0.11  2.13   0.74
*** OVERALL ***              14.31  26.75            0.81         0.96     0.88             0.96             0.80          0.55          0.10  5.45   0.94
```

Some basic control of the formatting of this table is possible via the `--n_digits` and `--table_format` flags. The former controls the number of decimal places printed for floating point numbers, while the latter controls the table format. For a list of valid table formats plus example outputs, consult the [documentation](https://pypi.python.org/pypi/tabulate) for the `tabulate` package.


### References
1. [`dscore` library](https://github.com/nryant/dscore)

