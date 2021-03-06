# TSAGen
Repository for paper ''TSAGen: Synthetic Time Series Generation for KPI Anomaly Detection''

TSAGen is undergoing a refactor and some features are currently unavailable. We are sorry for this inconvenience.

This repository will be maintained for a long time. Fell free to submit a issue or contact us if you encounter any issue when using TSAGen.

# Installation
```bash
git clone https://github.com/AprilCal/TSAGen.git
cd ./TSAGen
pip install requirements.txt
python setup.py install
```

## API Usage
You can directly use the API provided by TSAGen. The source file ''gen.py'' contains many examples for the API usage.
For example:
```python
# Choose the generator for season, noise, and trend component, respectively.
season_generator = sg.NormalSeasonGenerator(10,10,200,drift_a=0,drift_f=0,forking_depth=7)
noise_generator = ng.Gaussian()
trend_generator = tg.TrendGenerator()

# Control the noise component as a variable.
season = season_generator.gen_season()
length = len(season[0])
noise = [noise_generator.gen(0,sigma,length) for sigma in np.linspace(0.5,2,100)]
trend = trend_generator.gen(0,0,length)

# Assemble components.
assembler = assem.AssemblerWithAdditiveAnomalyInjector_v1(season,noise,trend,'noise',10e-7,0.2,a_type='beat')
assembler.assemble()
```
## Define a new type of anomaly
There are hooks reserved for extension. You can easily define a new type of anomaly by extending the AbstractGenerator.
For example, if you want to define a noise anomaly, you can just extend the class 'Pearson' and implement the \_inject() function, just like:
```python
class PearsonWithChangePoints(Pearson):
    def _inject(self):
    	# anomaly position
        pos_list = [0.5,0.8]
        # anomaly length
        a_len = 20
        for pos in pos_list:
            position = int(pos*len(self.noise))
            # Calculate anomaly noise segment.
            a_segment = np.random.normal(self.mu, self.sigma*10, a_len)
            # Inject anomaly and change the label.
            self.noise[position:position+a_len] = a_segment
            self.label[position:position+a_len] = np.ones(len(a_segment),dtype=np.int)
```

# Notes
Note that, to enable the pearson distribution, the installation of MATLAB and other configurations are required.
Please install Matlab and run the following commands.
```bash
cd matlabroot/extern/engines/python
python setup.py install
```

