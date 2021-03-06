# Part-of-Speech Tagging

Our part-of-speech tagger uses the generalized model of our previous approach, dynamic model selection, which is no longer used because the generalized model by itself performs as well as the dynamic model selection approach for most real-life applications and takes only 50% of memory and disk space. It processes about 68K tokens per second on an Intel Xeon 2.30GHz machine and shows state-of-the-art accuracy, especially on out-of-domain data. See [how to run command-line tools](../quick_start/command_line_tools.md) for more details about decoding.

* [Fast and Robust Part-of-Speech Tagging Using Dynamic Model Selection](http://aclweb.org/anthology-new/P/P12/P12-2071.pdf), Jinho D. Choi, Martha Palmer, Proceedings of the 50th Annual Meeting of the Association for Computational Linguistics (ACL'12), 363-367, Jeju, Korea, 2012.

## Training

### Configuration

The following shows a sample configuration file: [config\_train_pos.xml](https://github.com/clir/clearnlp/blob/master/src/main/resources/configure/config_train_pos.xml) (see [how to train](../quick_start/train.md)).

```
<configuration>
    <language>english</language>

    <global>
        <distributional_semantics>brown-rcv1.clean.tokenized-CoNLL03.txt-c1000-freq1.txt.xz</distributional_semantics>
    </global>

    <reader type="tsv">
        <column index="2" field="form"/>
        <column index="4" field="pos"/>
        <column index="5" field="feats"/>
    </reader>

    <pos>
        <trainer algorithm="adagrad" type="svm" labelCutoff="4" featureCutoff="3" alpha="0.02" rho="0.1" bias="0" average="false"/>
        <ambiguity_class_threshold>0.4</ambiguity_class_threshold>
        <document_frequency_cutoff>2</document_frequency_cutoff>
        <document_size>1500</document_size>
        <bootstraps>true</bootstraps>
    </pos>
</configuration>
```

| Element | Description |
| :-----: | :---------- |
| `<pos>` | Specifies the configuration for part-of-speech tagging.<ul><li>`ambiguity_class_threshold`: collect only ambiguity classes whose likelihoods are greater than this threshold (exclusive).</li><li>`document_frequency_cutoff`: use features extracted from only words whose document frequencies are greater than this cutoff (exclusive).</li><li>`document_size`: consider this many number of trees as one document.</li></ul> |

### Feature template

The following shows a sample feature template file: [feature\_en_pos.xml](https://github.com/clir/clearnlp/blob/master/src/main/resources/features/feature_en_pos.xml) (see [feature template](../formats/feature_template.md)).

```
<!--Feature template for POS tagging in English -->
<feature_template>
	<!-- 1-gram features -->
	<feature f0="i:f2"/>
	<feature f0="i-2:f2"/>
	<feature f0="i-1:f2"/>
	<feature f0="i+1:f2"/>
	<feature f0="i+2:f2"/>

	<feature f0="i:a"/>
	<feature f0="i-3:p"/>
	<feature f0="i-2:p"/>
	<feature f0="i-1:p"/>
	<feature f0="i+1:a"/>
	<feature f0="i+2:a"/>
	<feature f0="i+3:a"/>

	<feature f0="i:f4"/>
	<feature f0="i-1:f4"/>
	<feature f0="i+1:f4"/>

	<!-- 2-gram features -->
	<feature f0="i-2:f3" f1="i-1:f3"/>
	<feature f0="i-1:f3" f1="i:f3"/>
	<feature f0="i:f3"   f1="i+1:f3"/>
	<feature f0="i-1:f3" f1="i+1:f3"/>
	<feature f0="i+1:f3" f1="i+2:f3"/>

	<feature f0="i-2:p" f1="i-1:p"/>
	<feature f0="i+1:a" f1="i+2:a"/>
	<feature f0="i-1:p" f1="i+1:a"/>

	<!-- 3-gram features -->
	<feature f0="i-2:p" f1="i-1:p" f2="i:a"/>
	<feature f0="i-1:p" f1="i:a"   f2="i+1:a"/>
	<feature f0="i-2:p" f1="i-1:p" f2="i+1:a"/>
	<feature f0="i-1:p" f1="i+1:a" f2="i+2:a"/>

	<!-- affix features -->
	<feature f0="i:pf2"/>
	<feature f0="i:pf3"/>

	<feature f0="i:sf1"/>
	<feature f0="i:sf2"/>
	<feature f0="i:sf3"/>
	<feature f0="i:sf4"/>

	<!-- orthographic features -->
	<feature f0="i:orth"/>

	<!-- distributional semantics features -->
	<feature f0="i:dsw0"/>
	<feature f0="i-1:dsw0"/>
	<feature f0="i+1:dsw0"/>

	<!-- binary features -->
	<feature f0="i:b0" note="input is the first token"/>
	<feature f0="i:b1" note="input is the last  token"/>
</feature_template>
```