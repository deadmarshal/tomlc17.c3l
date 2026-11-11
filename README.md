# tomlc17.c3l

[tomlc17](https://github.com/cktan/tomlc17) binding for C3

# Usage
``` c3
module tomlc_example;

import std::io;
import std::os;
import tomlc17;

fn void error(ZString msg,ZString msg1)
{
  io::eprintfn("ERROR: %s%s\n",msg,msg1 ? msg1 : (ZString)"");
  os::exit(1);
}

fn int main(String[] args)
{
  Result result = tomlc17::parse_file_ex("simple.toml");

  if(!result.ok)
  {
    error((ZString)&result.errmsg,null);
  }

  defer tomlc17::free(result);
  
  tomlc17::Datum host = tomlc17::seek(result.toptab,"server.host");
  tomlc17::Datum port = tomlc17::seek(result.toptab,"server.port");

  if(host.type != tomlc17::Type.STRING)
  {
    error("missing or invalid 'server.host' property in config",null);
  }

  io::printfn("server.host = %s",host.u.s);

  if(port.type != tomlc17::Type.ARRAY)
  {
    error("missing or invalid 'server.port' property in config",null);
  }

  io::print("server.port = [");

  for(int i = 0; i < port.u.arr.size; i++)
  {
    tomlc17::Datum elem = port.u.arr.elem[i];
    if(elem.type != tomlc17::Type.INT64)
    {
      error("server.port element not an integer",null);
    }
    io::printf("%s%d", i ? ", " : "",(int)elem.u.int64);
  }
  io::printn("]");

  tomlc17::Datum dummy = tomlc17::seek(result.toptab,"server.dummy");

  if(dummy.type != tomlc17::Type.UNKNOWN)
  {
    io::printfn("Error: dummy element exists");
    os::exit(1);
  }  
  return 0;
}
```

