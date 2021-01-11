
## Logstash

### Generating dummy data

``` python
import numpy as np
import json
from datetime import datetime, timedelta
import os

if __name__ == "__main__":
    start_date = "18/09/19 01:00:00"
    next_date = datetime.strptime(start_date, '%d/%m/%y %H:%M:%S')

    num_periods = 2
    days_per_period = 2
    data_points_per_period = 1000

    seconds_per_data_point = days_per_period * 24 * 3600 / float(data_points_per_period)
    radians_per_data_point = 2 * np.pi / float(data_points_per_period)

    output_file = os.path.join(os.getcwd(), "TrigData.json")

    with open(output_file, "w") as file_obj:
        for i in range(num_periods*data_points_per_period+1):

            x = i*radians_per_data_point
            data = {"Sin":
                          {"x0": {"1": np.sin(x), "2": np.sin(2*x), "3": np.sin(3*x)},
                           "x1": {"1": x * np.sin(x), "2": x * np.sin(2*x), "3": x * np.sin(3*x)},
                           "x2": {"1": x * x * np.sin(x), "2": x * x * np.sin(2*x), "3": x * x * np.sin(3*x)}
                           },
                    "Cos":
                          {"x0": {"1": np.cos(x), "2": np.cos(2 * x), "3": np.cos(3 * x)},
                           "x1": {"1": x * np.cos(x), "2": x * np.cos(2 * x), "3": x * np.cos(3 * x)},
                           "x2": {"1": x * x * np.cos(x), "2": x * x * np.cos(2 * x), "3": x * x * np.cos(3 * x)}
                           },
                    "tan":
                          {"x0": {"1": np.tan(x), "2": np.tan(2 * x), "3": np.tan(3 * x)},
                           "x1": {"1": x * np.tan(x), "2": x * np.tan(2 * x), "3": x * np.tan(3 * x)},
                           "x2": {"1": x * x * np.tan(x), "2": x * x * np.tan(2 * x), "3": x * x * np.tan(3 * x)}
                           },
                    }
            data_structure = {"Data": data, "@timestamp": next_date.isoformat(), "PortalHost": "MV41"}
            json.dump(data_structure, file_obj)
            file_obj.write("\n")
            next_date = next_date + timedelta(seconds=seconds_per_data_point)


```

To illustrate the elastic pipeline, we will generate some JSON data for some trig functions (sin, cos and tan). The data will be parsed by Logstash, passed into elastic and plotted by Kibana.

We have 

### Logstash config and parsing data

```
input {
	tcp {
		port => 5000
		codec => json
	}
}


filter {
	mutate {
		lowercase => [ "host" ]
	}
	mutate { remove_field => [ "port"] }
}


output {

	stdout {
		codec => rubydebug
	}
	
	elasticsearch {
		index => "trig_functions_%{host}"
	}

}
```

``` shell
net
```

## Kibana

Make sure elasticsearch and kibana are running then point a web browser at:
http://localhost:5601/



Go to stack management / Index Patterns to create a new index pattern

