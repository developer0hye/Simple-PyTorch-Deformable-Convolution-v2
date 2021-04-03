# PyTorch-Simple-DCNv2
Don't feel fain to use Deformable Convolution


```python
import torch
import torchvision.ops
from torch import nn

class DeformableConv2d(nn.Module):
    def __init__(self,
                 in_channels,
                 out_channels,
                 kernel_size=3,
                 stride=1):

        super(DeformableConv2d, self).__init__()

        self.padding = kernel_size//2
        
        self.offset_conv = nn.Conv2d(in_channels, 
                                     2 * kernel_size * kernel_size,
                                     kernel_size=kernel_size, 
                                     padding=self.padding, 
                                     stride=stride,
                                     bias=True)
        
        self.modulator_conv = nn.Conv2d(in_channels, 
                                     1 * kernel_size * kernel_size,
                                     kernel_size=kernel_size, 
                                     padding=self.padding, 
                                     stride=stride,
                                     bias=True)
        
        
        self.conv = nn.Conv2d(in_channels=in_channels,
                              out_channels=out_channels,
                              kernel_size=kernel_size,
                              stride=stride,
                              padding=self.padding,
                              bias=True)
        
    def forward(self, x):
        offset = self.offset_conv(x)
        modulator = torch.sigmoid(self.modulator_conv(x))
        
        x = torchvision.ops.deform_conv2d(input=x, 
                                          offset=offset, 
                                          weight=self.conv.weight, 
                                          bias=self.conv.bias, 
                                          padding=self.padding,
                                          mask=modulator)
        return x
```