from pythermalcomfort.models import pmv_ppd,a_pmv,e_pmv,pmv,set_tmp,utci
from pythermalcomfort.models import pet_steady,vertical_tmp_grad_ppd,ankle_draft
from pythermalcomfort.utilities import v_relative #注意，官方文档是错的这里，包的位置不对
from pythermalcomfort.utilities import met_typical_tasks 
from pythermalcomfort.utilities import clo_individual_garments,clo_dynamic
from pythermalcomfort.psychrometrics import t_mrt
import math
import numpy as np
tdb = 27 # dry-bulb air temperature , [◦C] 干球温度（正常用温度计测得的温度，气温，注意测温时不要被太阳直晒到温度计）
tr = 25 # mean radiant temperature , [◦C]  平均辐射温度（这里直接给了一个，但是实际算的时候需要测量）
v = 0.1 # average air velocity , [m/s]  平均风速，用风速仪测得的值
rh = 50 # relative humidity , [%]  相对湿度，这里是比例，百分数前的


# partcipant ’s activity description  人体相关参数
activity = "Typing"  #activity即人体活动类型，与代谢参数met有关
garments = ["Sweatpants", "T-shirt", "Shoes or sandals"] #衣服的相关参数

#计算tr的补充参数
tg = 28 #globe temperature, [°C] 黑球温度，黑球测温器测得的温度,
d = 0.15 #diameter of the globe, [m] default 0.15 m 黑球测温器中黑球的直径，通常取0.15
emissivity=0.95 #e missivity of the globe temperature sensor 0.95 黑球测温器的发射率，通常取0.95
standard = 'Mixed Convection' #标准类型 standard ({“Mixed Convection”, “ISO”}) 一般取Mixed Convection，最新的成果
                              #either choose between the Mixed Convection and ISO formulations. 
# The Mixed Convection formulation has been proposed by Teitelbaum E. et al. (2022) to better determine the free and forced convection coefficient used in the calculation of the mean radiant temperature. 
# They also showed that mean radiant temperature measured with ping-pong ball-sized globe thermometers is not reliable due to a stochastic convective bias [22]. 
# The Mixed Convection model has only been validated for globe sensors with a diameter between 0.04 and 0.15 m.
tr = t_mrt(tg, tdb, v, d=0.15, emissivity=0.95, standard='Mixed Convection') #计算平均辐射温度
#同一测点平均辐射温度一般比空气温度高，平均辐射温度一般不能直接测得，需要通过空气温度、黑球温度、风速计算得到。
# print(tr)
